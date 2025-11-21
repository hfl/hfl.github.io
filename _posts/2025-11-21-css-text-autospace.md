---
layout: post
title: CSS的text-autospace属性
categories: css
date: 2025-11-21 07:30:34 +0800
---

CSS 4 开始属性 `text-autospace` 允许指定中日韩（CJK）字符和非中日韩字符之间的间距行为。各浏览器逐步实现对该属性的[支持](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference/Properties/text-autospace#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9%E6%80%A7)。

## 语法
~~~css
text-autospace: normal;
text-autospace: no-autospace;
text-autospace: ideograph-alpha;
text-autospace: ideograph-numeric;
text-autospace: punctuation;
text-autospace: insert;
text-autospace: replace;
text-autospace: ideograph-alpha ideograph-numeric punctuation;
text-autospace: ideograph-alpha ideograph-numeric;
text-autospace: ideograph-alpha ideograph-numeric insert;
text-autospace: auto;

/* 全局值 */
text-autospace: inherit;
text-autospace: initial;
text-autospace: revert;
text-autospace: revert-layer;
text-autospace: unset;
~~~

## 值

normal
: 创建默认行为，自动在 CJK 字符与非 CJK 字符之间以及标点符号周围添加间距。此值的效果等同于同时应用 ideograph-alpha 和 ideograph-numeric。

`<autospace>`
: 提供对间距行为的更精细控制。支持关键字 no-autospace，或组合使用 ideograph-alpha、ideograph-numeric 和 punctuation 中的一种或多种，可选地跟随 insert 或 replace。

  no-autospace
  : 禁用 CJK 和非 CJK 字符间的自动间距行为。
 
  ideograph-alpha
  : 仅在表意文字（如片假名和汉字）与非表意字母（如拉丁字母）之间添加间距。不会在表意文字与非表意数字之间添加间距。

  ideograph-numeric
  : 仅在表意文字（如片假名和汉字）与非表意数字（如拉丁数字）之间添加间距。不会在表意文字与非表意字母之间添加间距。

  punctuation
  : 根据特定语言的排版规范，在标点符号周围添加不可分割的间距。

  insert
  : 仅当表意文字与非表意文字之间不存在现有空格时，才添加指定的间距。

  replace
  : 将表意文字与非表意文字之间的现有间距（例如 U+0020）替换为指定的间距。

auto
: 允许浏览器选择符合排版规范的间距。不同浏览器和平台间的间距可能存在差异。
