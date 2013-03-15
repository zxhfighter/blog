---
layout: post
categories: javascript
title: JavaScript Quiz(附说明)
---

铛铛铛铛~~~~，上一篇[JavaScript Quiz](http://zxhfighter.github.com/blog/javascript/2013/03/14/javascript-quiz.html)的说明篇准时赴约啦，答案就写在了代码的中间，用`//=>`表示输出或者返回结果，在每段代码的后面，针对每个quiz都有单独的说明。

## 关于arguments

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：该IIFE返回什么结果？
// PS: IIFE（Immediately-Invoked Function Expression），即自调用函数、立即执行函数
(function(){
   return typeof arguments;  // => 'object'
})();

// quiz-2：该IIFE返回什么结果？
(function(){
   return Object.prototype.toString.call(arguments).slice(8, -1);  // => 'Arguments'(Chrome)，'Object'(IE8)
})();

// quiz-3：该IIFE返回什么结果？
(function(){
   return Object.prototype.toString.call([].slice.call(arguments)).slice(8, -1);  // => 'Array'
})(1,'2',new Date(),4);

// quiz-4：console.log输出什么？
function b (x, y, a) {
    arguments[2] = 10;
    console.log(a);
}
b(1,2,3);  // => 10

// quiz-5：f()输出什么？
function f(x, y) {
    x = 10;
    console.log(
       arguments[0],
       arguments[1]
    );
}
f();  // => undefined, undefined
</pre>

上面的几个quiz主要考察了arguments对象，该对象为JavaScript中的内置对象，包含了函数所有要调用的参数，表现上来看像一个数组（有getter和setter方法），实际上只是一个特殊的对象。另外，在ES5中，arguments.callee为undefined，所以尽量少用arguments.callee属性吧。

* quiz-1: arguments并不是数组，typeof由于对对象类型探测的能力有限，此处只返回了一个笼统的`'object'`
* quiz-2: 为了获取一个对象的真实类型，可以调用`Object.prototype.toString.call`方法，由此我们看到了arguments的真面目，即一个Arguments类型的对象（在IE8下返回为`'Object'`）
* quiz-3：由于arguments并不是一个数组，有时我们需要将它转化成一个真正的数组，可以用`[].slice.call`方法，不过该方法有潜在的性能问题。
* quiz-4：arguments有getter和setter，即能够改变实参的值。
* quiz-5：结果为什么不是10, undefined呢？

## 关于函数表达式

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：typeof g()结果为？
var f = function g() { return 42; };
typeof g();  // => ReferenceError: g is not defined

// quiz-2：下面的IIFE返回什么？
(function(f){
   return typeof f();  // => 'number'
})(function(){ return 1; });

// quiz-3：typeof f的值？
var f = (function f() {
    return '1';
}, function g() {
    return 2;
})();
typeof f;  // => 'number'
</pre>

上面几个quiz考察了函数表达式。函数有两种形式，一种为函数声明，一种为函数表达式。通过函数声明定义的函数在作用域范围内一直可见，而函数表达式只有在给变量赋值时，该函数变量才会有生效。

* quiz-1：这段代码将一个命名函数表达式赋值给变量f，`f.name = 'g'`，该语句运行完后，g不复存在，不过在函数内部还是可以访问g，因为在函数内部总是可以使用函数名字
* quiz-2：将一个函数当作参数传递给了IIFE，该函数执行完毕后返回1，`typeof 1`，返回`'number'`
* quiz-3：首先计算逗号表达式，返回后面一个函数g，之后执行函数g，执行返回2，`typeof 2`，返回`'number'`

## 关于变量声明和运算顺序

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：x的值？
var y = 1, x = y = typeof x;
x  // => 'undefined'

// quiz-2：a的值？
var a = 1;
var b = function a (x) {
    x && a(--x);
};
console.log(a); // => 1

// quiz-3：下面表达式的值？
(1, 5-1) * 2 // => 8

// quiz-4：下面几条语句输出？
var b = 10,
    c = (
            20,
            function(x) {return x + 100},
            function() {return arguments[0]}
        );
a = b + c
({x:10}).x  // => 20
</pre>

上面几个quiz考察了变量提升、逗号表达式等相关知识。

* quiz-1：注意变量提升、由右至左的运算顺序
* quiz-2：在前面提到过，命名函数表达式赋值给变量后，函数名a就不复存在了，输出的a为上面var定义的a
* quiz-3: 逗号表达式总是返回最后一个逗号的结果
* quiz-4：其实即`a = b + c({x: 10}).x`，这下很清楚了吧

## 关于变量提升

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出结果
var abc = 'global_var';
(function(){
    console.log(typeof abc);     // => 'undefined'
    console.log(typeof fun_1);   // => 'function'
    console.log(typeof fun_2);   // => 'undefined'
    var abc = 'local_var';       
    function fun_1() {}
    var fun_2 = function (){};
    console.log(typeof abc);     // => 'string'
    console.log(typeof fun_1);   // => 'function'
    console.log(typeof fun_2);   // => 'function'
})(window);

// quiz-2：x的值？
var x = 1;
if (function f(){}) {
    x += typeof f;  
}
x  // => '1undefined'

// quiz-3：iife返回结果？
(function(){
   function f() { return 1; }
   return f();
   function f() { return 2; } 
})(window);  // => 2

// quiz-4：a的值？
function a (x) {
    return x * 2;
}
var a;
console.log(a); // => function a(x) { return x*2; }

// quiz-5：iife返回结果？
(function(){
   a = function() { return 1; };
   function a () {
        return 2;
   }
   var a;
   return a;
})(window);  // => function () { return 1; }
</pre>

上面的几个quiz主要考察了变量提升。

* quiz-1：这段代码的实际运行结果示意如下面的代码，结果不言自明。

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出结果
var abc = 'global_var';
(function(){
    var abc;
    var fun_2;
    function fun_1() {}
    console.log(typeof abc);
    console.log(typeof fun_1);
    console.log(typeof fun_2);
    abc = 'local_var';
    fun_2 = function (){};
    console.log(typeof abc);
    console.log(typeof fun_1);
    console.log(typeof fun_2);
})(window);
</pre>

* quiz-2：在条件判断条件里面的f，不会在函数作用域声明该函数，所以结果为'1undefined'
* quiz-3：函数声明提升，后面的函数f覆盖了前面的函数f，返回2
* quiz-4：变量提升之后，函数a覆盖了变量a，因此输出为函数
* quiz-5：等同于如下代码：

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-5：iife返回结果？
(function(){
   var a;
   function a () {
        return 2;
   }
   a = function() { return 1; };
   return a;
})(window);  // => function () { return 1; }
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
   console.log(typeof global_var);        // => 'number'
   console.log(typeof global_var_imply);  // => 'undefined'
   console.log(typeof local_var);         // => 'number'
   console.log(typeof x);                 // => 'number'
   console.log(arr);                      // => [1,2,undefined,4,5] 
   console.log(arr.length);               // => 5
   console.log(obj);                      // => {x:1, z:3}
})(1);
</pre>

上面的几个quiz考察了delete运算符，delete用来删除对象的属性，记住三点：

1. 通过var定义的变量是不能被delete的，例如global_var、local_var变量，而隐式定义的全局变量global_var_imply可以被delete
2. 对象的属性删除只能用delete，不能将其值设为null或undefined
3. 删除数组的一项，不会改变数组长度，只是将该值置为undefined；删除数组项，最好使用数组的splice方法。

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
   return typeof arguments[0](); // => 'undefined'
})(foo.bar);

(function(){
   return typeof arguments[0].call(foo);  // => 'number'
})(foo.bar);


// quiz-2：typeof返回结果？
var foo = {
    bar: function () {
        return this.baz;
    },
    baz: 1
};
typeof (f = foo.bar)();  // => 'undefined'

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
    foo.bar(),             // => 20
    (foo.bar)(),           // => 20
    (foo.bar = foo.bar)(), // => 10
    (foo.bar, foo.bar)()   // => 10
);

// quiz-4：写出所有输出
function Foo() {
  this.name = 'foo';
}
var x = Foo();
var y = new Foo();
x.name // => TypeError: Cannot read property 'name' of undefined
y.name // => 'foo'
</pre>

上面几个quiz对JavaScript神出鬼没的this进行考察。this能用于五种不同的情形，且this的指向各不相同：
1. 全局范围：指向全局对象，一般指向window
2. 函数调用：指向全局对象
3. 方法调用：指向调用方法的对象
4. 构造函数调用：指向用该构造函数创建的对象
5. 通过call和apply显示设置：被显示设置的对象

* quiz-1：第一个IIFE，将方法传入后失去了对象，退化成一个普通的函数，this指向全局对象，而全局对象没有baz属性，因而返回'undefined'；第二个IIFE，显示设置了this为foo，而`foo.baz = 1`，所有typeof 1返回`'number'`
* quiz-2：有点类似quiz-1，将一个对象的方法赋值给变量，将退化成普通函数，this指向全局对象
* quiz-3：函数经过赋值运算，逗号运算后退化成普通函数，this指向全局对象，而this.x = 10
* quiz-4：函数调用，this指向全局对象；构造函数调用，this指向新创建的对象

## 关于typeof

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出下面几个typeof的结果
var x = [typeof x, typeof y][1];
typeof typeof x;  // => 'string'
typeof typeof(null);  // => 'string'
typeof foo == 'undefined';  // => true
typeof foo === 'undefined';  // => true
</pre>

上面一个quiz考察了typeof，不过typeof检测对象类型真心太弱，一般只用来检测未定义的变量之类的。

* quiz-1：注意两点：typeof typeof x是必然返回'string'的；typeof的优先级比==和===高。

## 关于JSON对象

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：iife的返回结果?
(function(foo){
   return typeof foo.bar;  // => 'undefined'
})({foo: {bar: 1}});
</pre>

上面考察了对象的基本知识，本题比较简单，实际上通过`foo.foo.bar`就可以获取正确的值。

## 关于类和对象

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：true or false?
function F() { return F; }
new F() instanceof F  // => false

// quiz-2：下面运行结果输出什么？
100.toString.length  // => SyntaxError: Unexpected token ILLEGAL
100['toString']['length']; // => 1
100 .toString();  // => 100
(100).toString();  // => 100
1..z; // => undefined
</pre>

上面考察了类和对象的一些知识。

* quiz-1：通过new F()创建的对象返回F，而返回的F会丢失constructor信息（静态属性等），`new F() instanceof Function`返回为true
* quiz-2：`100['toString']['length']`首先将100转化成对象new Number(100)，由于Number的toString方法只接受一个radix参数，所有返回为1；`1..z`为什么是undefined，试试`1..toString`你就明白

## 关于with

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：输出结果
with (function(x, undefined){}) length // =>2
</pre>

上面考察了with，不过在实际的项目中不推荐使用with，新的标准也不推荐使用with，上面的quiz等同于下面的代码：

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：输出结果
with (function(x, undefined){}) {
    (function(x, undefined){}).length
}
</pre>

## 关于++

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：result的值？
var num1 = 5, num2 = 6, result = num1+++num2;
result // 
</pre>

上面考察了++用法，一般也推荐比较少使用++，而使用x += 1之类的代替，在JSHint中，默认对++和--还会提示警告。

quiz-1：这让我想起了学C语言时，谭浩强书里的一个例子，直接打击了我学C语言的积极性，鄙视这样写代码的人，不过言归正传，知道这里是这样运行的即可：`result = (num1++) + num2`

## 关于作用域

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：a的值？
if (!("a" in window)) {
    var a = 1;
}
console.log(a);
</pre>

额，这道题知道全局变量和局部变量的区别，就没有问题了，呵呵。

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
o.doIt();  // => 5
</pre>

上面的考察了setTimeout和闭包，需要注意的是在setTimeout中的匿名函数的this指代全局变量，所以返回为5.

## 关于字符串和数字的比较和转换

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出
var num1 = '10',
    num2 = '9';
console.log(num1 < num2);  // => true
console.log(+num1 < num2); // => false
console.log(num1 + num2);  // => 109
console.log(+num1 + num2); // => 109
</pre>

上题注意字符串和数字的转化、字符串的比较、数字的比较即可。

##关于字符串截断

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出
var msg = 'Hello, world';
msg.substring(1,4)
msg.substr(1,4)
msg.slice(1,4)
</pre>

这题无话可说，属于hello, world级别的题目了。

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

console.log(obj + 1); // => 11
obj < "9" // => false
</pre>

上题考察了对象的valueOf方法和toString方法，上面两种情况都调用了对象的valueOf方法。

## 关于parseInt

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：两个parseInt的值？
parseInt('06')  // => 6
parseInt('08')  // => 8(Chrome)，0(IE)
 </pre>

上题考察了parseInt方法，需要注意的是，对于parseInt('08')，高级浏览器已经能够正常返回结果了，不过为了兼容，最好使用`parseInt('08', 10)`。

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

最后一题了，结果为undefined、30和20，原理想明白了吗？