title: 前端大杀器 Sublime 3安装和常用插件
date: 2016-01-26 20:19:14
categories: 工具
tags: [前端]
---
SublimeText是一款非常精巧的文本编辑器，适合编写代码、做笔记、写文章。它用户界面十分整洁，功能非同凡响，性能快得出奇。这些非常棒的特性 包括任意跳转（Goto Anything）、多重选择（multiple selections）、指令面板（command palette）、免打扰模式（distraction free mode）、分区编辑（split editing）、快速项目切换（instant project switch），你还可以随意地自定义更多功能。还有，这款编辑器支持Mac、Windows和Linux平台。
<!-- more -->

### 安装
1.官网下载最新版，可以选择text2或者text3，我选择的text3，它出来也很久啦，很多方面已经得到了完善。
2.度娘一下激活码，大多都是可以用的，当然不用激活码也没事，它是无限试用的。
3.有需要可以汉化，其实也没必要，就是一些菜单而已。也不难，网上随意搜下汉化包放到指定位置即可。

### 插件
SublimeText本身已经非常强大，但是更棒的是有一长串的插件支持它，给它带来更强大的功能。本文将介绍一些 Sublime Text 3 支持的热门插件（Sublime Text 2 的一些插件在 Sublime Text 3 上不支持）。

#### Package Control
Package Control 插件是一个方便 Sublime text 管理插件的插件，但因为 Sublime Text 3 更新了 Python 的函数，API不同了，导致基于 Python 开发的插件很多都不能工作，Package Control 原来的安装方法都失效了。

网上有2种使用 Git 的安装方法，感觉太麻烦了。此处将 wbond 网站的 ST3 Package Control 简便安装方法翻译转至此处，方便大家查阅。
##### 简单的安装方法：
从菜单 View - Show Console 或者 ctrl + ~ 快捷键，调出 console。将以下 Python 代码粘贴进去并 enter 执行，不出意外即完成安装。以下提供 ST3 和 ST2 的安装代码：
Sublime Text 3：

    import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())`

Sublime Text 2：

    import urllib2,os; pf='Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler( ))); open( os.path.join( ipp, pf), 'wb' ).write( urllib2.urlopen( 'http://sublime.wbond.net/' +pf.replace( ' ','%20' )).read()); print( 'Please restart Sublime Text to finish installation')

#### Emmet
Emmet 是一个前端开发的利器，其前身是 Zen Coding。它让编写 HTML 代码变得简单。Emmet 的基本用法是：输入简写形式，然后按 Tab 键。
安装请使用上面的插件哦！

关于 Emmet 的更多用法，请看[官方文档](http://docs.emmet.io/)，这份[速查表](http://docs.emmet.io/cheat-sheet/)可以帮你快速记忆简写形式。

#### Soda Theme
Soda Theme 是最受欢迎的 Sublime Text 主题。
现在Soda集成了Light和Dark，以前是要分开下的呢。话不多说，上使用方法：
在你的配置文件（菜单 Preferences -> Settings - User）中加入"theme": "Soda Light.sublime-theme" 或 "theme": "Soda Dark.sublime-theme"

#### SublimeLinter
SublimeLinter 是一个代码校验插件，它可以帮你找出错误或编写不规范的代码，支持 C/C++、CoffeeScript、CSS、Git Commit Messages、Haml、HTML、Java、JavaScript、Lua、Objective-J、Perl、PHP、Puppet、Python、Ruby 和 XML 语言。

SublimeLinter 默认以 background 模式运行，在用户输入的同时即时校验，如果你想要 Sublime Text 运行得更流畅，可以空白处右键改为 load-save 模式或 save-only 模式，在读取和保存是校验或只在保存时校验。

#### Sublime​Code​Intel 
Sublime​Code​Intel 是一个代码提示、补全插件，支持 JavaScript、Mason、XBL、XUL、RHTML、SCSS、Python、HTML、Ruby、Python3、XML、Sass、XSLT、Django、HTML5、Perl、CSS、Twig、Less、Smarty、Node.js、Tcl、TemplateToolkit 和 PHP 等语言，是 Sublime Text 自带代码提示功能的很好扩展。它还有一个功能就是跳转到变量、函数定义的地方，十分方便。

#### Alignment
Alignment 是一个代码格式化插件，它可以使多行代码中的等号对齐，也可以调整多行代码为一个缩进级别，默认快捷键是 ctrl+alt+a（Mac OS 上是 cmd+ctrl+a）。跟QQ截图冲突了？自己改下吧，我改成了ctrl+shift+a。

#### ColorPicker
在编辑CSS样式的时候，要加个自己喜欢颜色或改改颜色啥的，要到PS里去调色？ColorPicker 可以让 Sublime Text 3内置一个调色盘，调好颜色，点击OK就会在光标处生成十六进制颜色代码。

#### CssComb
这是用来给CSS属性进行排序的格式化插件。如果你想保持的代码干净整洁，并且希望按一定的顺序排列（是不是有点强迫症了？），那么这个插件是一种有效解决的方案。特别是当你和其他有自己代码编写风格的开发者一同协作的时候。

#### ConvertToUTF8
Sublime原生不支持GBK等编码，打开这类编码文件时中文或一些字符都乱码了，安装该插件即可解决

#### HTML5&HTMLattributes
这两个插件对弥补了sublime对html5支持度不足的缺陷

#### Jquery
顾名思义，包含jquery函数

#### CodeFormatter
可以对html、JS、CSS、PHP、python代码格式化的插件

默认快捷键ctrl+alt+F，如果想对PHP格式化，需要到该插件默认设置中设定php.exe的路径

#### BracketHighLighter
括号、标记高亮等等，挺好用的，只是偶尔识别错误

#### DocBlockr
DocBlockr 可以使你很方便地对代码建立文档。它会解析函数，变量，和参数，根据它们自动生成文档范式，你的工作就是去填充对应的说明。

#### JsFormat
一个JS代码格式化插件。

#### MarkdownEditing
此篇博客用到了该插件

#### OmniMarkupPreviewer
用来预览markdown的插件，相同功能的有Markdown Preview