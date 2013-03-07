---
layout: post
categories: javascript
title: 前端问题札记（一）
---

如何调试IE浏览器独有的BUG？
---

有时候，由于浏览器兼容性问题或者你写的脚本不够健壮，在IE下会产生各种奇怪的BUG，这时候就如何调试呢？

如何在Windows下查看某个端口是否开启，被占用进程是哪个？
---
例如查看8080端口的占用情况，运行cmd，输入netstat -ano | findstr "8080"，找到pid为5873，然后tasklist | findstr "5873"

setTimeout给函数传递参数时，在IE下报错：参数无效
---

将带参数的函数用匿名函数包起来，然后传给setTimeout

常见linux命令
---
source
echo
ps
grep
awk
xargs
kill
print
rm
svn co
svn update
mv
cp
sed
ant
sleep
sh
tail
head
cd
exit
tee

关于排序
---
对于sort方法，Chrome为了更快的排序方法，使用了不稳定的算法。而其他浏览器则保持一致，使用稳定的排序算法。

关于switch
---
switch 比较为强类型比较，注意类型转化

关于IE的innerHTML
---
在IE下面，如果改变一个已经隐藏的元素的innerHTML，然后再显示出来的时候，会导致页面刷新，跳转到兼容性视图，解决方案是待元素显示后，再改变innertHTML，待验证。

高级浏览器添加离线提示，可以使用window.webkitNotifications对象，需要先同意一个协议
---
<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>baidu.on( window, 'click', function(){
    if( window.webkitNotifications 
        && window.webkitNotifications.checkPermission() != 0 ) {
        window.webkitNotifications.requestPermission();
    }
} );
baidu.on( window, 'online', function(){} );
baidu.on( window, 'offline', function(){
    if( window.webkitNotifications
        && window.webkitNotifications.checkPermission() == 0 ) {
        var notification = window.webkitNotifications.createNotification( '', 
        '提醒', '您当前网络情况有点差，好像掉线了 ：）' );
        notification.show();
        notification = null;
    }
});
</code></pre>

`this.length >>> 0`表示什么?
--------------------------
这个可以看ECMAScript的相关章节，可以用来将：
1. 所有非数值转化成0，例如undefined
2. 所有大于等于0等数取整数部分

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>'string' >>> 0  //==>0
null >>> 0  //==>0
undefined >>> 0  //==>0
34.23 >>> 0  //==>0
[] >>> 0  //==>0
</code></pre>
参考：http://www.zhihu.com/question/20693429





