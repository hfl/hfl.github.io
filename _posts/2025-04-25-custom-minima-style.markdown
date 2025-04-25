---
layout: post
title:  "定制 Minima 主题的样式"
date:   2025-04-25 16:20:34 +0800
categories: minima
---

使用 Jekyll 默认的主题 Minima，整体还好，但是中文很多时，就会感觉过度挤压在
一起。看到[网站上的语言](https://www.w3.org/International/getting-started/language)的中文，
感觉就很舒服，特地看了一下字号，使用的 18px 的字，于是我想要调整一下 Minima
的字号。

于是，在网上搜寻相关资料，很少。找到寥寥几篇，好像也不太好用。最后测试下面
方法可以：

1. 在根目录下建文件夹 `assets`；
2. 在 `assets` 内创建文件 `main.scss`；
3. 在 `main.scss`内添加 frontmatter 的短横，但是不需要添加内容；
4. 添加 `@import "minima";`
5. 接着添加您自己的定制 CSS 代码即可。

我的 CSS 定制样式内容如下：

~~~ css
---
# 只是 main Sass 文件需要 front matter （只是短横即可）
---
@import "minima";
body {
  font-size: 18px;
  line-height: 1.8em;
}
.post-content > p {
  text-indent: 2em;
}
~~~

上面的代码是增大字号和行距，下面代码是使段落开始要后退两个字，这些都是中文
的习惯——现在，看起来就好多了！
