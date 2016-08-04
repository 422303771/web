<!-- toc -->

- [Atom](#atom)
	- [安装atom](#安装atom)
	- [atom packages](#atom-packages)
		- [atom-chrome](#atom-chrome)
		- [git-plus](#git-plus)
		- [markdown-pdf](#markdown-pdf)
		- [markdown-preview-plus](#markdown-preview-plus)
		- [markdown-writer](#markdown-writer)
		- [tidy-markdown](#tidy-markdown)
		- [emmet](#emmet)
		- [jshint](#jshint)
		- [minimap](#minimap)
		- [pigments](#pigments)
		- [markdown-preview-enhanced](#markdown-preview-enhanced)
			- [mermaid 渲染 flowchart 和 sequence diagram](#mermaid-渲染-flowchart-和-sequence-diagram)
			- [KaTex 渲染数学表达式](#katex-渲染数学表达式)
			- [PlantUML 渲染图形](#plantuml-渲染图形)
			- [WaveDrom 来渲染 digital timing diagram.](#wavedrom-来渲染-digital-timing-diagram)
			- [Viz.js 来渲染 dot language 图形.](#vizjs-来渲染-dot-language-图形)
			- [reveal.js 来渲染漂亮的 presentations](#revealjs-来渲染漂亮的-presentations)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# Atom

[官网](atom.io)

## 安装atom

```
$ sudo add-apt-repository ppa:webupd8team/atom
$ sudo apt-get update
$ sudo apt-get install atom
```

## atom packages

### atom-chrome

### git-plus

### markdown-pdf

### markdown-preview-plus

### markdown-writer

### tidy-markdown

### emmet

html快速编写工具

### jshint

js语法插件

### minimap

与sublime相似的代码预览

### pigments

### markdown-preview-enhanced

[markdown-preview-enhanced](https://github.com/shd101wyy/markdown-preview-enhanced/blob/master/docs/README_CN.md)

#### mermaid 渲染 flowchart 和 sequence diagram

[mermaid 文档](http://knsv.github.io/mermaid/#flowcharts-basic-syntax)

#### KaTex 渲染数学表达式

需要学习LaTeX语法

[KaTeX](https://github.com/Khan/KaTeX/wiki)

[参考1](http://mlworks.cn/posts/introduction-to-mathjax-and-latex-expression/)

$$\oint_C x^3\, dx + 4y^2\, dy 2 = \left( \frac{\left(3-x\right) \times 2}{3-x} \right)$$

$$\sum_{m=1}^\infty\sum_{n=1}^\infty\frac{m^2\,n} {3^m\left(m\,3^n+n\,3^m\right)}$$

$$\phi_n(\kappa) = \frac{1}{4\pi^2\kappa^2} \int_0^\infty \frac{\sin(\kappa R)}{\kappa R} \frac{\partial}{\partial R} \left[R^2\frac{\partial D_n(R)}{\partial R}\right]\,dR$$

![sp160804_133226](http://i.imgur.com/PzFRo0S.png)

<!-- ![sp160804_133226](/assets/sp160804_133226.png) -->

#### PlantUML 渲染图形

[PlantUML](http://zh.plantuml.com/)

#### WaveDrom 来渲染 digital timing diagram.

[WaveDrom](http://wavedrom.com/tutorial.html)

使用json样式文本

```wavedrom
{signal: [
  {name:'clk', wave: 'p.....PPPPp....' },
  {name:'dat', wave: 'x....2345x.....', data: 'a b c d' },
  {name:'req', wave: '0....1...0.....' }
],
head: {text:
  ['tspan',
    ['tspan', {class:'error h1'}, 'error '],
    ['tspan', {class:'warning h2'}, 'warning '],
    ['tspan', {class:'info h3'}, 'info '],
    ['tspan', {class:'success h4'}, 'success '],
    ['tspan', {class:'muted h5'}, 'muted '],
    ['tspan', {class:'h6'}, 'h6 '],
    'default ',
    ['tspan', {fill:'pink', 'font-weight':'bold', 'font-style':'italic'}, 'pink-bold-italic']
  ]
},
foot: {text:
  ['tspan', 'E=mc',
    ['tspan', {dy:'-5'}, '2'],
    ['tspan', {dy: '5'}, '. '],
    ['tspan', {'font-size':'25'}, 'B '],
    ['tspan', {'text-decoration':'overline'},'over '],
    ['tspan', {'text-decoration':'underline'},'under '],
    ['tspan', {'baseline-shift':'sub'}, 'sub '],
    ['tspan', {'baseline-shift':'super'}, 'super ']
  ],tock:-5
}
}
```

![sp160804_133234](http://i.imgur.com/6fP55cx.png)

<!-- ![sp160804_133234](/assets/sp160804_133234.png) -->

#### Viz.js 来渲染 dot language 图形.

[dot](https://en.wikipedia.org/wiki/DOT_(graph_description_language))

```viz
graph ethane {
        C_0 -- H_0 [type=s];
        C_0 -- H_1 [type=s];
        C_0 -- H_2 [type=s];
        C_0 -- C_1 [type=s];
        C_1 -- H_3 [type=s];
        C_1 -- H_4 [type=s];
        C_1 -- H_5 [type=s];
}
```
![sp160804_133243](http://i.imgur.com/FVuu5Tw.png)

<!-- ![sp160804_133243](/assets/sp160804_133243.png) -->

#### reveal.js 来渲染漂亮的 presentations

幻灯片样式

[presentations](https://rawgit.com/shd101wyy/markdown-preview-enhanced/master/docs/presentation-intro.html)
