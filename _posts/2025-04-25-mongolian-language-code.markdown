---
layout: post
title:  "网页中的蒙古语言代码设置"
date:   2025-04-25 06:20:34 +0800
categories: mongolian
---

蒙古语是一种民族语言，隶属于阿尔泰语系蒙古语族。现代世界中蒙古人还有使用蒙
古语，主要使用群体为中国的蒙古族、蒙古国的蒙古人以及俄罗斯的卡尔梅克等以蒙
古族为主体的俄联邦成员国等。而现在使用的蒙古文则主要分为传统蒙古文（回鹘蒙
古文，主要使用群体为中国境内的蒙古族）和西里尔蒙古文（中国境外的蒙古族使用）。
就蒙古语而言大体相同，不同方言的词汇有些许差异；而蒙古文则差异较大：传统蒙古文
书写模式为行内从上到下书写，换行从左到右换行，而西里尔蒙古文由于采用了斯拉夫
字母，所以书写模式为常见的行内从左到右书写，换行从上到下换行。

所以，在同为使用蒙古文书写的蒙古语文献中会出现较大的反差。在数字文献中，为了
便于信息的交换和处理，就需要对使用的蒙古文进行标注。

在网页的编写中，有一个语言标签（lang）就是用来标注所使用的语言文字系统的。
该标签的完整标注形式为：

  language-extlang-script-region-variant-extension-privateuse
 
1. language：语言标签，通常使用  ISO 639 定义的语言代码，而且是使用宏语言代码。
2. extlang：语言扩展标签，有些语言会有很多方言或者其他语言变种，通常会在这里
规范控制。
3. script：脚本标签，就是书写文字的代码。
4. region：地区，对应前面标签中提到的语言文字系统使用的地区。
5. variant：变体，变体子标签是用于指示前面的语言、脚本和区域子标签组合尚未涵
盖的方言或脚本变体的值。变体子标签必须出现在任何语言、脚本或区域子标签之后，
但前面不一定需要有脚本或区域子标签。除非您在专业领域工作，否则您不太可能需要
使用变体子标签。
6. extension 和 private-use：这两个标签用的时候很少，具体可以参考[这里](https://www.w3.org/International/articles/language-tags/)

[The IANA registry](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry) 注册的语言标签有关蒙古语的有：

~~~~
Type: language
Subtag: mn
Description: Mongolian，宏语言定义
Added: 2005-10-16
Scope: macrolanguage

Type: language
Subtag: cmg
Description: Classical Mongolian，传统蒙古语
Added: 2009-07-29

Type: language
Subtag: khk
Description: Halh Mongolian，方言
Added: 2009-07-29
Macrolanguage: mn

Type: language
Subtag: msr
Description: Mongolian Sign Language，应该是盲文
Added: 2009-07-29

Type: language
Subtag: mvf
Description: Peripheral Mongolian，方言
Added: 2009-07-29
Macrolanguage: mn

Type: language
Subtag: xgn
Description: Mongolian languages，蒙古语族
Added: 2009-07-29
Scope: collection

Type: language
Subtag: xng
Description: Middle Mongolian，中世纪蒙古语，蒙古帝国统治下的地区使用蒙古语
Added: 2009-07-29

Type: script
Subtag: Mong
Description: Mongolian，回鹘蒙古文字
Added: 2005-10-16

Type: redundant
Tag: mn-Cyrl
Description: Mongolian in Cyrillic script，西里尔蒙古文字
Added: 2005-02-17

Type: redundant
Tag: mn-Mong
Description: Mongolian in Mongolian script，回鹘蒙古文字
Added: 2005-02-17
~~~~

参考 BCP47，关于蒙古语文的定义有：`mn-Mong` 和 `mn-Cryl`，如果加上地区，就是
`mn-Mong-CN` 和 `mn-Cryl-MN`。实际上，使用那面的代码就可以区分书写系统了。

通过书写系统的区分，就可以根据不同的书写系统来定义对应的 CSS 代码来个性化定制
显示。
