# bootstrap CSS 基本样式

一些说明，需要使用`HTML5`文档类型。

bootstrap 使用的栅格系统进行页面布局

[bootstrap 官方网址](http://getbootstrap.com/)

[bootstrap中文网址](http://v3.bootcss.com/)

[w3schools 网址](http://www.w3schools.com/bootstrap/default.asp)

## bootstrap 栅格系统

![屏幕快照 2016-09-27 23.29.31](http://ooo.0o0.ooo/2016/09/27/57ea1ff486368.png)

<!-- ![屏幕快照 2016-09-27 23.29.31](</assets/屏幕快照 2016-09-27 23.29.31.png>) -->

`.`为class ，`#`为ID 。

栅格每行的最大数量为12

* `.col-xs-*` 超小屏幕 `< 768px` (手机)
* `.col-sm-*` 小屏幕 `≥ 768px` (平板)
* `.col-md-*` 中等屏幕 `≥ 992px` (1080P显示器)
* `.col-lg-*` 大屏幕 `≥ 1200px`  (2K 显示器)

上的四种`class`可以一起使用。

`.row` 将`col-*-*`栅格标识为一行。

`.container`固定宽度(现代web中很少使用)，`.container-fluid`100%宽度。

`.container` 与 `.container-fluid` 一般写在`row上层。

列偏移使用 `.col-md-offset-`



### 嵌套列

![屏幕快照 2016-09-27 23.55.58](http://ooo.0o0.ooo/2016/09/27/57ea26235c100.png)

<!-- ![屏幕快照 2016-09-27 23.55.58](</assets/屏幕快照 2016-09-27 23.55.58.png>) -->

```HTML
<div class="row">
  <div class="col-sm-9">
    Level 1: .col-sm-9
    <div class="row">
      <div class="col-xs-8 col-sm-6">
        Level 2: .col-xs-8 .col-sm-6
      </div>
      <div class="col-xs-4 col-sm-6">
        Level 2: .col-xs-4 .col-sm-6
      </div>
    </div>
  </div>
</div>
```

### 列排序

通过使用 `.col-md-push-*` 和 `.col-md-pull-*` 类就可以很容易的改变列（column）的顺序。

![屏幕快照 2016-09-27 23.58.23](http://ooo.0o0.ooo/2016/09/27/57ea26b02fa63.png)

<!-- ![屏幕快照 2016-09-27 23.58.23](</assets/屏幕快照 2016-09-27 23.58.23.png>) -->

### 使用`.panel`

`.panel`可以用来对网站进行快速布局。
