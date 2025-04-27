---
layout: post
title:  "CSS Media Query 和 HTML 的 viewport 元标签"
date:   2025-04-27 07:30:34 +0800
categories: css
---

自适应网页设计（Responsive Web Design）躲不开一个话题就是媒体查询（Media Query）和 Meta viewport 设置。

随着数字设备的多样化，各种输出设备曾出不穷，所以，CSS 增加了一个媒体查询（Media Query）功能，用来针对不同的媒体优化输出。我们这里只讨论屏幕（Screen）输出情况。屏幕有大有小，针对横向书写的内容，就需要关心屏幕的宽度（Width）。如果只是使用一栏来显示内容，则问题不大，但是好的设计往往会使用多栏设计。这就导致有些内容要么变得非常小，要么拥挤不堪。媒体查询会根据获得的屏幕宽度（Device-width），来重新排版内容，进而输出到屏幕，以使用户获得最佳体验。

媒体查询可以指定或者限定宽度，例如

~~~ css
@media screen and (max-width: 800px) {
  text-color: red
}
~~~

上面代码说明在 800px 以下，文字颜色为红色。同理如果使用了竖向书写模式，这里就应该设定条件为高度 height，相应的限定条件就是 max-height 和 min-height。

这里需要说明：这里的 width 和 height 不能使用逻辑属性 inline-size 和 block-size。也就是说逻辑属性可以在 `[ ]` 中使用，但是不能在 `( )` 中使用。

另外一个设定就是 viewport 元标签。这是在网页 head 部分的一个元标签。用来设定在小屏显示时，需不需要缩放。例如：

~~~ html
<meta name="viewport" content="width=device-width, initial-scale=1" />
~~~

如果使用了媒体查询的断点设计，则对小屏将进行了优化，那么就不需要对网页进行缩放。如果不设置，则在小屏移动设备上查看时，整个网页会缩小显示全部，如果放大，则需要来回拉动滚动条查看。这个设置对桌面计算机不起作用，只对移动设备有用，甚至只对苹果手机有用。
