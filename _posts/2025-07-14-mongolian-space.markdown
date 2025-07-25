---
layout: post
title: 蒙古文空格
categories: mongolian
date: 2025-07-14 7:30:34 +0800
---

蒙古文中有一个很特别的空格——这个空格并不是单词的边界，而是起到连接单词附
加成分的作用；这个空格宽度要少于普通空格。因为其很特别，所以我们可以叫它为
蒙古文空格。

在进行蒙古文 Unicode 编码时，具有蒙古文空格一样作用的一个字符编码已经存在，
就是**窄宽度不间断空格**（Narrow No-Break Space），根据 Unicode 委员会不重复
编码的原则，就决定了蒙古文可以借用该码位，作为蒙古文空格使用。所以，在早期
的蒙古文编码及相关转换规则中就借用了该码位做为连接后缀的专用字符。

在使用过程中，就发现因为该码位并不是蒙古文专用字符，在多文种混用时会造成使
用混乱的问题。于是在后续的标准中，就逐渐的使用了其他替代方案——例如使用蒙
古文元音间隔符（Mongolian Vowel Separator, U+180E）来替代。在 2023 年出版的《
信息技术 传统蒙古文名义字符、变形显现字符和控制字符使用规则》中，已经使用蒙
古文元音间隔符代替窄宽度不间断空格。同时在新的标准中，还增加了第四个自由变
体选择符，也就是自由变体选择符由于原来的三个增加到了四个。

基于 Unicode 蒙古文输入法是一套复杂的系统，输入法+字库+复杂文字处理中间件缺
一不可，相应的变动和更新可能会影响到整个系统的使用，所以需要随时关注动态，
密切系统联系，及时调整和完善系统运作才能流畅正确的使用。
