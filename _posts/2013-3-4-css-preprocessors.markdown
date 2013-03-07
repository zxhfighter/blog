---
layout: post
categories: javascript
title: css preprocessors
---

什么是CSS预处理器
-----------------
CSS预处理器是一种CSS的语法糖，这些语法糖将一些动态语言的特性，例如变量、函数、继承
添加进来，便于编写DRY的CSS代码。

目前有哪些比较有名的预处理器
----------------------------
目前业界使用的预处理器主要有三种：Less、Sass以及Stylus。

安装预处理器
----------------------------
Less可以在浏览器端运行，也可以运行在服务器端。

* 浏览器端：通过引入的less.js动态解析less文件，不过由于性能原因，不推荐。
* 服务器端：通过Node.js或者命令行工具[lessc](https://github.com/duncansmart/less.js-windows "lessc命令行工具")进行离线处理。

Sass的安装需要Ruby环境，然后使用`gem install sass`安装，如果你需要Sass的工具集Compass，可以同时安装`gem install compass`，个人认为，将sass结合compass使用，才能发挥预处理器的最大效力。

Stylus的安装也需要Ruby环境，然后使用`gem install stylus`即可。

安装好后，就需要选择一个好的编辑器或者IDE辅助开发，例如提供语法高亮，自动编译等功能，这里推荐使用ST2，在ST2中搜索相应的关键词，可以找到很多相关的插件、例如sass、sassbuild、less2css、stylus等插件。

预处理器语法
----------------------------
上文介绍的三种预处理器的语法有点差别，主要在于需不需要使用`{}`、`;`、`:`等符号等，此外在定义变量、函数、混入方面有些差别，具体的文章见[这里](http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/)。

下面以Sass为例，说明预处理器常见的功能，需要注意的是，Sass有两种语法，这里以新语法为例（文件名以.scss结尾）说明，另外，本文只是为了演示Sass能够做什么，所以本文中的许多例子就来自Sass的官网，未做特殊修改。

PS：如果你想跟着本文动动手，可以体验下最近比较流行的在线编辑工具，例如jsfiddle.net、http://codepen.io/等。

变量
----

变量能够定义常见的颜色、边距大小，以后修改就只需修改一处。

<?prettify lang=css linenums=true?>
<pre class="prettyprint"><code>//在Sass中，变量定义以$开头
$blue: #3bbfce;
$margin: 16px;

.content-navigation {
  border-color: $blue;
  color: darken($blue, 9%);
}

.border {
  padding: $margin / 2;
  margin: $margin / 2;
  border-color: $blue;
}
</code></pre>

嵌套
----

嵌套规则使代码更加易于书写和阅读。

<?prettify lang=css linenums=true?>
<pre class="prettyprint"><code>//嵌套规则更加符合人的思维，书写更加清晰
.nav {
    width: 200px;
    ul {
        list-style: none;
        li {
            background-color: #ccc;
            a {
                color: #000;
                /* & 表示直接父节点引用，这里为a */
                .ie7 & {
                    color: #666;
                }
            }
        }
    }
}
</code></pre>

混入
----

混入能够复用之前的CSS代码（属性和值），且混入能够传递参数，且能定义默认参数。

<?prettify lang=css linenums=true?>
<pre class="prettyprint"><code>@mixin table-base {
  th {
    text-align: center;
    font-weight: bold;
  }
  td, th {padding: 2px}
}

@mixin left($dist) {
  float: left;
  margin-left: $dist;
}

#data {
  @include left(10px);
  @include table-base;
}
</code></pre>

继承
----

继承同混入一样，能够复用之前的代码（属性和值），不过不能定义参数。

<?prettify lang=css linenums=true?>
<pre class="prettyprint"><code>.error {
  border: 1px #f00;
  background: #fdd;
}
.error.intrusion {
  font-size: 1.3em;
  font-weight: bold;
}

.badError {
  @extend .error;
  border-width: 3px;
}
</code></pre>

导入
-----

导入使用命令`import "ie6.scss"`，如果导入的后缀为`.css`，则同常规的css导入。

<?prettify lang=css linenums=true?>
<pre class="prettyprint"><code>/** 常规导入 **/
@import "reset.css";

/* 导入scss */
@import "ie6";

/* 导入{type}，{type}可为scss、less等 */
@import "ie7.{type}";
</code></pre>

函数
----

函数与混入有点类似，能够传入参数，一般用来计算属性的值。

<?prettify lang=css linenums=true?>
<pre class="prettyprint"><code>$grid-columns: 12;
$grid-width: 960px;

@function cal-col-width($cols) {
    @return (($grid-width / $grid-columns) * $cols / $grid-width) * 100%;
}

article {
    float: left;
    width: cal-col-width(8);
}

aside {
    float: right;
    width: cal-col-width(4);
}
</code></pre>

颜色函数
----

颜色函数提供了常见的颜色处理函数，例如反色、增量、变暗等等。

<pre class="prettyprint"><code>$color: #abcdef;

/* 增亮颜色 */
$lightColor: lighten($color, 20%);

/* 调暗颜色 */
$darkenColor: darken($color, 20%);

/* 反色 */
$invertColor: invert($color);

/* 互补色 */
$compleColor: complement($color);
</code></pre>

流程控制
----

流程控制，提供了条件判断、for循环、each语句等功能。

<pre class="prettyprint"><code>@for $i from 1 to 5 {
    .border-#{$i} {
        border: #{$i}px solid #000;
    }
}

$widths: 1 2 3 4;

@each $width in $widths {
    .border-#{$width} {
        border: #{$width}px solid #000;
    }
}
</code></pre>

谁正在使用CSS预处理器
---------------------

* [Twitter Bootstrap](https://github.com/twitter/bootstrap)
* [SyntaxHighlighter](https://github.com/alexgorbatchev/SyntaxHighlighter)
* [lightbox2](https://github.com/lokesh/lightbox2)
* [点点](http://www.zhihu.com/question/20020362)

不同的声音
----------------------
尽管看起来CSS预处理器能够提高CSS的质量，但还是有部分人对使用预处理器持观望态度，主要认为团队使用预处理器会带来额外的学习成本，此外预处理器的功能在他们看来也不是特别的强大。

* [为什么我一直反对使用LESS或SASS](http://www.amberweinberg.com/why-im-still-against-sass-less/)
* [LESS到底好不好](http://www.zhihu.com/question/20259365)

参考资料
----------------------

* [sass vs less vs stylus](http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/)
* [an introduction to less and comparison to sass](http://coding.smashingmagazine.com/2011/09/09/an-introduction-to-less-and-comparison-to-sass/)
* [使用compass](http://www.ruanyifeng.com/blog/2012/11/compass.html)







