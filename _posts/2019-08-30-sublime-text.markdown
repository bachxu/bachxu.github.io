---
layout: post
title:  "编辑器 Sublime Text"
date:   2019-08-30 10:44:37 +0800
categories: iTools
---
[Sublime Text](https://www.sublimetext.com/) 是一款兼顾简洁与高效的跨平台编辑器，其拥有的包管理器以及成千上万的插件为开发者提供了福音。开发者可以根据自己的喜好和习惯来单独进行配置。

> Write the code, Change the world.

### 安装包管理

[Package Control](https://packagecontrol.io/installation) 非常方便管理 Sublime Text 插件，通过 Package Control 可以便捷高效管理编辑器的插件。

安装方法：在编辑器自带的终端中输入以下命令进行安装。

{% highlight shell %}
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
{% endhighlight %}

在 macOS 系统下通过使用组合键 `command + shift + p` 可以查看当前编辑器中所有安装的插件。

### 推荐插件

基础插件

- BracketHighlighter：一个高亮识别成对符号位置的插件；
- GitGutter：Git 版本差异标志；
- ini：查看 .ini 文件；
- Alignment：代码格式化插件，可以使多行代码中的等号对齐，也可以调整缩进；
- AllAutocomplete 搜索所有打开的文件来寻找匹配的提示词；
- SublimeLinter 代码校验插件，帮助找出错误或者编写不规范的代码；
- SublimeCodeInter 一款代码提示、补全的插件；

Python 开发

- Pylinter 检查Python语法插件，需要结合[Pylint](http://pylint.org/)使用；

前端开发

- Emment：前端开发利器插件，让编写HTML代码变得简单；
- Babel：javascript(ES6)及jsx的代码高亮，安装完成后需将文件设置成 `Babel -> JavaScript(Babel)`；
- JsFormat：js 文件自动排版，安装完成后需在配置文件中进行以下配置；

{% highlight javascript %}
{
  "e4x": true,
  // jsformat options
  "format_on_save": true,
}
{% endhighlight %}

### 主题配置

Sublime Text 自带有一些主题，也可以通过 Package Control 像插件一样安装主题。[Theme - Soda](https://packagecontrol.io/packages/Theme%20-%20Soda) 是个人比较喜欢的一款主题配置，成功安装后需要修改用户自定义配置项来激活主题，在 `Preferences -> Settings - User` 文件中添加以下内容：

{% highlight javascript %}
{
  "theme": "Soda Dark 3.sublime-theme"
}
{% endhighlight %}

为了营造更舒服的视觉效果，还可以配搭使用相应的颜色。这边与之搭配的是 [Monokai Extended](https://packagecontrol.io/packages/Monokai%20Extended) 颜色。另外可以通过安装 [Markdown Extended](https://packagecontrol.io/packages/Markdown%20Extended) 插件来增加 Markdown 语言的视觉效果。

### 博主常用的用户自定项

- tab_size：缩进大小；
- translate_tabs_to_spaces：将所有tabs转为空格；
- trim_trailing_white_space_on_save：保存文件时去除多余的空格；
- ensure_newline_at_eof_on_save：在文件保存时确保文件地步有新起一行；
- word_wrap：文本自动换行；
- scroll_past_end：编辑器最后留白滚动；
- highlight_modified_tabs：高亮修改的tabs；
- find_selected_text：显示查询选中的文本内容；
- draw_white_space：显示空格。

Follow your heart.
