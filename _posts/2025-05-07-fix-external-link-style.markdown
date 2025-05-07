---
layout: post
title:  "修正网站的外部链接样式"
date:   2025-05-07 16:30:34 +0800
categories: css external-link
---

链接有很多种，但是从链接对象的位置分类，无非就是网站的内部链接和外部链接。如果你的网站很有特点，当链接到网站的外部资源时，有必要提醒用户“正在离开这个有趣的网站”。那么怎么提醒呢？

就可以利用链接的不同样式来提醒，例如添加一个小窗口，意思通过这个窗口——“温馨提示：您将驶离我站。”

实现方法就是通过 `a` 标签的 `href` 属性是否以 `http` 开始判断。这里有个前提——是你**不要随便使用 `http` 开始的值，内部链接一律使用站内相对资源定位，只有站外资源启用绝对定位**。

实现代码如下：

~~~css
a[href^="http"] {
  background: url("external-link.svg") no-repeat 100% 0;
  background-size: 16px 16px;
  padding-right: 19px;
}
~~~

效果如下：非常感谢您访问我的[网站](/)，想要学习更多的网站建设相关知识请移步 [Mozilla 的 MDN](https://developer.mozilla.org/zh-CN/)。
