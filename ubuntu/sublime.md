<!-- toc -->

 - [sublime text 3](#sublime-text-3)

  - [插件](#插件)

    - [package control](#package-control)
    - [AutoFileName](#autofilename)
    - [BracketHighlighter](#brackethighlighter)
    - [Color Highlighter](#color-highlighter)
    - [ConvertToUTF8](#converttoutf8)
    - [Git](#git)
    - [JsFormat](#jsformat)
    - [Laravel Blade Highlighter](#laravel-blade-highlighter)
    - [MarkdownEditing](#markdownediting)
    - [Material Theme](#material-theme)
    - [OmniMarkupPreviewer](#omnimarkuppreviewer)
    - [Sass](#sass)

  - [问题与解决方案](#问题与解决方案)

    - [问题 1：OmniMarkupPreviewer不能预览](#问题-1omnimarkuppreviewer不能预览)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# sublime text 3

记录下在sublime text 3使用中一切

## 插件

### package control

sublime下插件管理工具`package control`

[官方网址](https://packagecontrol.io/)

安装方法<kbd>Ctrl</kbd>+<kbd>`</kbd>复制下方代码到出现的命令栏后，按<kbd>Enter</kbd>。

```
import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

安装完成后，使用<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>，可以看到如下界面。

![sp160804_143016](/assets/sp160804_143016.png "package control控制台")

<!-- ![sp160804_143016](http://i.imgur.com/X58cMcu.png) backup -->

 ### AutoFileName

自动补全项目目录下文件名。

### BracketHighlighter

自动标记前后标签

### Color Highlighter

高亮颜色，只在css，lass，sass中有效。

### ConvertToUTF8

自动转换文本编码为UTF8

### Git

Git插件

### JsFormat

Javascript语句格式化

### Laravel Blade Highlighter

laravel语法高亮

### MarkdownEditing

markdown编辑器

### Material Theme

风格

<!-- ![sp160804_152313](/assets/sp160804_152313.png) -->

 ![sp160804_152313](http://i.imgur.com/4fjyI6y.png)

### OmniMarkupPreviewer

markdown在浏览器中预览

### Sass

Sass语法高亮

--------------------------------------------------------------------------------

## 问题与解决方案

### 问题 1：OmniMarkupPreviewer不能预览

错误如下图：

![sp160804_143504](/assets/sp160804_143504.png)

<!-- ![sp160804_143504](http://i.imgur.com/g6TELHi.png) backup -->

 **解决方法**

打开`Sublime Text > Preferences > Package Settings > OmniMarkupPreviewer > Settings - User`

填入下方内容后保存：

```
{
    "renderer_options-MarkdownRenderer": {
        "extensions": ["tables", "fenced_code", "codehilite"]
    }
}
```

--------------------------------------------------------------------------------
