---
layout: post
title:  "CSS 逻辑属性"
date:   2025-04-26 0:0:34 +0800
categories: css
---

CSS 早期都是针对实体进行绝对方位、长度的设计，后来随着各种文本布局的出现，情
况变得复杂起来，原来的设计逐渐不能应对新情况了。于是，提出了逻辑属性的概念，
方位等也开始使用相对概念。这些逻辑属性并不是新概念，而是对原来实体的概念进行
了重新解释和设计，与原有的设计并不冲突，只需要进行改写即可。

这些逻辑属性主要分为三方面：

1. 用于浮动和定位的逻辑属性；
2. 用于外边距、边框和内边距的逻辑属性；
3. 逻辑尺寸属性；

下面分别列出对应关系。

## 用于浮动和定位的逻辑属性

逻辑属性与逻辑值规范包含了 `float` 和 `clear` 的实体值的逻辑对应关系，以及用
于定位布局的定位属性的逻辑对应关系。

# 属性的和值的对应关系

下表详列了本指南所论述的属性和值，以及其实体对应关系。假设书写模式（writing-mode）为横排，行内方向为从左到右。

| 逻辑属性或逻辑值 |	实体属性或实体值 |
|:------------------|------------------:|
|float: inline-start |	float: left|
|float: inline-end |	float: right|
|clear: inline-start |	clear: left|
|clear: inline-end |	clear: right|
|inset-inline-start |	left|
|inset-inline-end |	right|
|inset-block-start |	top|
|inset-block-end |	bottom|
|text-align: start |	text-align: left|
|text-align: end |	text-align: right|

除了这些有对应关系的属性，另有可以处理块向和行向尺度的简写属性。除 inset 属性外，这些属性没有与实体属性的对应关系。

| 逻辑属性 |	用途 |
|:----------|------:|
|inset-inline |	为行向尺度同时设置上述两个偏移值。|
|inset-block |	为块向尺度同时设置上述两个偏移值。|
|inset |	同时设置四个偏移值，多值语法使用实体对应关系。|

## 用于外边距、边框和内边距的逻辑属性

逻辑属性与逻辑值规范为各种外边距、边框和内边距属性及其简写属性定义了相对于流的对应关系。如果你看过 CSS 逻辑属性与逻辑值的汇总页面就会看到那里列了一大堆属性。这主要是因为外边距、边框和内边距每个都有四个全称属性，还有所有的简写属性。

# 外边距、边框和内边距的对应关系

规范详述了每个逻辑值到实体值的对应关系。假设用的是 horizontal-tb 的书写模式（writing-mode）和从左到右的行内方向，我在下面的表格里给出了这些对应的属性。这时行向是水平方向——从左到右——而且 margin-inline-start 会等价于 margin-left。

如果你用了 horizontal-tb 的书写模式和从右到左的行内方向，那么 margin-inline-start 会和 margin-right 一样，而在竖排书写模式里 margin-inline-start 会和用 margin-top 一样。

| 逻辑属性 | 	实体属性 |
|:---------|-----------:|
|border-block-end 	|border-bottom |
|border-block-end-color 	|border-bottom-color|
|border-block-end-style 	|border-bottom-style|
|border-block-end-width 	|border-bottom-width|
|border-block-start 	|border-top|
|border-block-start-color 	|border-top-color|
|border-block-start-style 	|border-top-style|
|border-block-start-width 	|border-top-width|
|border-inline-end 	|border-right|
|border-inline-end-color 	|border-right-color|
|border-inline-end-style 	|border-right-style|
|border-inline-end-width 	|border-right-width|
|border-inline-start 	|border-left|
|border-inline-start-color 	|border-left-color|
|border-inline-start-style 	|border-left-style|
|border-inline-start-width 	|border-left-width|
|border-start-start-radius 	|border-top-left-radius|
|border-end-start-radius 	|border-bottom-left-radius|
|border-start-end-radius 	|border-top-right-radius|
|border-end-end-radius 	|border-bottom-right-radius|
| margin-block-end 	| margin-bottom|
| margin-block-start 	| margin-top|
| margin-inline-end 	| margin-right|
| margin-inline-start 	| margin-left|
| padding-block-end 	| padding-bottom|
| padding-block-start 	| padding-top|
| padding-inline-end 	| padding-right|
| padding-inline-start 	| padding-left|

还有一些简写属性可以用于让我们同时设置盒子块向或者行向的两侧。这些简写属性没有等价的实体属性。

| 属性 |	用途 |
|:-----|-------:|
| border-block |	为两个块向边框设置 border-color、border-style 和 border-width。|
| border-block-color |	为两个块向边框设置 border-color。|
| border-block-style |	为两个块向边框设置 border-style。|
| border-block-width |	为两个块向边框设置 border-width。|
| border-inline |	为两个行向边框设置 border-color、border-style 和 border-width。|
| border-inline-color |	为两个行向边框设置 border-color。|
| border-inline-style |	为两个行向边框设置 border-style。|
| border-inline-width |	为两个行向边框设置 border-width。|
| margin-block |	设置所有块向外边距（margin）。|
| margin-inline |	设置所有行向外边距（margin）。|
| padding-block |设置块向内边距（padding）。|
| padding-inline |	设置行向内边距（padding）。|

## 逻辑尺寸属性

实体尺度属性和逻辑属性用于在页面上设置元素尺寸，而我们将在本指南中解释两者之间相对于流的对应关系。

在指定元素的尺寸时，标示实体尺寸参照的是水平和竖直方向（如左和右）的实体尺度，而逻辑属性与逻辑值规范使你可以参照文本流向（行向与块向）标示尺寸。虽然对于我们中的大多数而言，这些相对于流的对应关系很可能都是默认的，但是在设计中你很可能会同时用到实体尺寸和逻辑尺寸。你可能想要某些特性在任何书写模式里参照的都是实体尺度。

# 尺度的对应关系

下表提供了逻辑属性与实体属性的对应关系。这些对应关系假定你在 horizontal-tb 的书写模式里——例如英文或者阿拉伯文，此时 width 会对应到 inline-size。

假如你在竖排书写模式里，那么 inline-size 会对应到 height。

| 逻辑属性 | 实体属性 |
|:----------|----------:|
| inline-size |	width |
| block-size |	height |
| min-inline-size |	min-width |
| min-block-size |	min-height |
| max-inline-size |	max-width |
| max-block-size |	max-height |
