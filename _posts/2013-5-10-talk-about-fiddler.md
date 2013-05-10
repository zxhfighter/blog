---
layout: post
categories: javascript
title: 谈谈Fiddler
keywords: javascript,css,html,fiddler,tangram,blog,program,front-end
---

Fiddler，即小提琴，是一款闻名遐迩的Web调试代理工具。另外一个比较有名的工具为HttpWatch（分为免费版和收费版），提供了类似的功能，只能在IE和Firefox中使用。而Fiddler是免费的，且能监听电脑上所有的HTTP请求，不管你使用什么浏览器，还是使用联网的客户端软件。

Fiddler能够捕获你电脑与互联网之间的所有HTTP(S)请求，是一个HTTP请求和响应拦截器，并能对进出的HTTP请求和响应进行修改。

Fiddler由于能够监控所有HTTP请求，很适合前端攻城狮使用，当然也可以用它来学习HTTP的相关知识。

![Fiddler运行原理](/blog/assets/image/fiddler.png)

<div style="text-align:center;font-size:12px;color:#930;font-family:楷体;">Fiddler运行原理</div>

上图说明如下：

1. 发送一个请求，例如：http://www.google.com.cn/?q=jack，Fiddler能够查看该请求详细信息
2. Fiddler能够在该请求到达服务器之前对其进行修改，例如参数改成：http://www.google.com.cn/?q=rose，即下文要讲的伪造请求
3. 从服务器获取响应后，Fiddler能够查看该响应详细信息。这里假设响应为JSON数据`{"success": true}`，表示执行成功
4. Fiddler能够在该请求到达浏览器之前对其进行修改，例如改成`{"success": false}`，这样就变成执行失败了，下文的反向代理功能将对其进行详细讲述

在使用的过程中，总结了几个比较常用的功能。

* 查看请求：例如发送方式为GET还是POST，发送的参数是啥，是否带有cookie等。
* 查看响应: 例如该响应是否为缓存信息，响应的类型，压缩方式，以及响应的内容等。
* 反向代理：可以将一个响应用本地文件来代理，便于前端调试。
* 伪造请求：可以修改一个请求的头部、参数等信息，然后发送给后端，这种方式可以用来秒杀哦。
* 数据缓存：可以通过自定义规则，从网站拉取资源，最常见的就是一个AJAX应用的各种JSON响应。
* 快速检索：通过命令行能够快速检索到所需要的请求。
* 过滤规则：能够自定义过滤规则，只监听符合条件的请求和响应。

## 查看请求

这个大家应该都很熟悉了，Chrome和FireFox相关开发工具的网络面板都能查看请求的详细信息，包括请求方式（GET或POST）、请求头部（伪造UA）、请求参数（是否编码）等等。

`GET /static/ver/c2adfc72f69e04ee.extern_src.min.js?v=0.1.0 HTTP/1.1`

## 查看响应

同上，Fiddler同Chrome开发工具和Firebug，都能很方便的查看响应的详细信息，包括响应状态（成功失败）、响应头部（缓存策略）、压缩编码（gzip或deflate）、响应内容等。

`HTTP/1.1 200 OK`

## 反向代理

反向代理这里指Fiddler的AutoResponder功能，能够在本机上用本地文件替换成线上文件，从而解决了线上代码难以调试的问题。

需要注意的是，AutoResponder只能实现文件粒度的反向代理，不能实现文件夹粒度的反向代理。如果需要反向代理整个文件夹，可以使用功能强大的ngnix等反向代理服务器。

反向代理截图如下：

![autoresponder](/blog/assets/image/autoresponder.jpg)

<div style="text-align:center;font-size:12px;color:#930;font-family:楷体;">反向代理</div>

上图中的URL匹配规则一般选择正则匹配（regex:）或者完整匹配（exact:）。

## 伪造请求

有两种方式可以构造伪造的请求：

* 可以在左侧面板将要伪造的请求拖到Composer面板
* 在请求上右键，选择`Replay -> Reissue from Composer`

![composer](/blog/assets/image/composer.jpg)

之后你就可以在Parsed面板和Raw面板随意篡改了，譬如请求头部，譬如请求参数等等。

## 数据缓存

数据缓存有几种方式：

* 在一个请求上右键，选择`Save -> Response -> Response Body`，可以保存单个响应
* 在一个请求上右键，选择`Data storage`可以按照规则文件保存单个响应
* 通过菜单`Rules -> Customize Rules`修改规则文件，之后选中`Rules -> Data storage`，就可以批量保存任何响应

![DataStorage](/blog/assets/image/datastorage.png)

## 过滤规则

过滤器通过指定过滤规则，只侦听符合条件的请求和响应，该功能目前使用不多。

## 命令行

命令行常见的命令如下：

* ?search: 例如输入?.png，查找所有png图片
* >size: 例如输入>4k，查找响应大于4k的所有请求
* =[method|status code]: 例如输入=POST，查找所有POST请求；输入=404，查找所有响应为404的请求
* @host: 例如输入@cdn，查找host中带有cdn的所有请求
* 断点命令等

## 相关参考资料

* [Fiddler官网](http://fiddler2.com/documentation)
* [Fiddler教程汇总](http://kb.cnblogs.com/page/130367/)
* [小坦克的博客](http://www.cnblogs.com/TankXiao/)
* [Fiddler中文使用教程](http://www.ipmtea.net/css_tools/201110/09_711.html)
* [使用Fiddler拉取数据桩](http://maojs.com/posts/fiddler-script.html)
* [Fiddle2命令行汇总](http://fiddler2.com/documentation/KnowledgeBase/QuickExec)
* [前端开发利器-Fiddler](http://www.cnblogs.com/yuzhongwusan/archive/2012/07/20/2601306.html)