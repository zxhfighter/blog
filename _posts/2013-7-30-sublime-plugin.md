---
layout: post
categories: javascript
title: 编写你的第一个sublime插件
---

Sublime编辑器具有很强的扩展能力，你可以为其编写自动完成、代码片段，插件，宏等等，来提高工作效率。本文介绍你如何从头开始编写一个插件，由于插件的语言使用Python，因此你需要稍微了解Python语言。

## 开发插件之前

在开发第一个插件之前，可以先下载TortoiseSVN插件，然后到Packages目录找到TortoiseSVN文件夹，熟悉下里面的文件以及内容，了解一个完整的插件编写完成后的样子，有个感性认识。另外，在Packages目录下的Defualt文件夹也有很多插件的例子，值得参考，例如：

* Packages/Default/duplicate_line.py：复制一行或者单词
* Packages/Default/goto_line.py：提示输入，定位到行
* Packages/Default/font.py：读取设置，调整字体
* Packages/Default/mark.py：侧边栏添加图标
* Packages/Default/trim_trailing_whitespace.py：保存前，去掉尾部空格

## 开发你的第一个插件

首先选择`Tools -> New Plugin...`菜单，可以看到新建了一个插件文件，代码如下：

<?prettify lang=python linenums=true?><pre class="prettyprint">
import sublime, sublime_plugin

# ExampleCommand名字中Command为必须的
# ExampleCommand将会转换成下划线风格example，又例如TortoiseSaveCommand会变成tortoise_save
class ExampleCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.insert(edit, 0, "Hello, World!")
</pre>

选择`Preferences -> Browser Packages...`，打开Packages文件夹，新建一个目录MyExample，然后将当前文件保存至该文件夹。

此时按下`CTRL + ``打开控制台，运行如下命令：

<?prettify lang=python linenums=true?><pre class="prettyprint">
view.run_command('example')
</pre>

此时在当前文件最前面就会看到Hello, World!几个字符了。

<?prettify lang=python linenums=true?><pre class="prettyprint">
Hello, World!import sublime, sublime_plugin

class ExampleCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.insert(edit, 0, "Hello, World!")
</pre>

恭喜你，出师告捷！下面来分析下代码。

第1行：引入了sublime和sublime_plugin两个模块，按下`CTRL + ``打开控制台，输入help(sublime)和help(sublime_plugin)可以查看模块中定义的对象以及它们的API，当然这些信息也可以从官方提供的[API页面](http://www.sublimetext.com/docs/2/api_reference.html)找到，E文不好的童鞋，这里还有一个[翻译版](http://ux.etao.com/posts/549)。

第3-5行：定义了一个类ExampleCommand，该类继承自sublime_plugin.TextCommand，下面的run方法就是在TextCommand中定义的。该类除了从TextCommand继承外，还可以从以下几个类继承：

* TextCommand: 通过View对象来访问选择的/缓存区的内容，最为常用
* WindowCommand: 通过Window对象来引用当前窗口
* ApplicationCommand: 既不能引用文字内容，也不能引用窗口，无视之
* EventListener: 文档发生改变时的事件处理，例如文件保存前，去掉每一行行尾的空格。这里可以查看Packages文件夹下Defualt文件夹下的trim_trailing_white_space.py的代码：

<?prettify lang=python linenums=true?><pre class="prettyprint">
import sublime, sublime_plugin

class TrimTrailingWhiteSpace(sublime_plugin.EventListener):
    def on_pre_save(self, view):
        if view.settings().get("trim_trailing_white_space_on_save") == True:
            trailing_white_space = view.find_all("[\t ]+$")
            trailing_white_space.reverse()
            edit = view.begin_edit()
            for r in trailing_white_space:
                view.erase(edit, r)
            view.end_edit(edit)

    # ...
</pre>

## 定义快捷键

插件编写好以后，你肯定不愿通过运行`view.run_command('example')`命令来调用插件，这时可以为你的插件绑定快捷键。

打开目录MyExample，新建文件Default (Windows).sublime-keymap，输入如下内容：

<?prettify lang=python linenums=true?><pre class="prettyprint">
[
    { "keys": ["alt+v"], "command": "example" }
]
</pre>

保存后，按下`alt+v`看看效果吧。

## 定义右键菜单

除了快捷键，还可以将命令添加进右键菜单、主菜单和侧边栏菜单。右键菜单在MyExample文件夹下新建文件Context.sublime-menu，内容如下：

<?prettify lang=python linenums=true?><pre class="prettyprint">
[
    { "caption": "-" },
    { "caption": "Hello,World!", "command": "example", "args": {}}
    { "caption": "-", "id": "end" }
]
</pre>

## 定义主菜单

主菜单文件为Main.sublime-menu，内容为：

<?prettify lang=python linenums=true?><pre class="prettyprint">
[{
    "id": "view",
    "children": [{
        "caption": "Hello,World!",
        "id": "hello,world",
        "command": "example"
    }]
}]
</pre>

## 定义侧边栏菜单

侧边栏文件为Side Bar.sublime-menu，内容为：

<?prettify lang=python linenums=true?><pre class="prettyprint">
[
    { "caption": "-" },
    { "caption": "Hello,world!", "command": "example", "args": {}}
    { "caption": "-", "id": "end" }
]
</pre>

## 发布插件

经过上述几个步骤，一个插件基本成型了，此时你可以将插件文件夹复制或者打包发送给需要的人，当然更好的做法是将插件发布到sublime的插件库，这些都是后话了。

## 总结

总结一下，Sublime插件的编写，最难的还是需要将插件API理解透彻，同时能够熟练的Python编程和好的IDEA，如果需要更深一步的学习的话，这里有一篇进阶[教程](http://net.tutsplus.com/tutorials/python-tutorials/how-to-create-a-sublime-text-2-plugin/)。
