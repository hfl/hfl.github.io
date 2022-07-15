---
title: kramdown（上）
categories: kramdown
date: '2017-03-24 09:33:25'
---

kramdown 不是 markdown，是对 markdown 的 基于 Ruby 语言的重写和扩展！

kram 与 mark 的关系？就是 reverse 的关系，反转。

下面详细介绍一下（主要翻译自[karmdown语法速览](https://kramdown.gettalong.org/quickref.html)）：

kramdown 主要有两类标记：块级和内联级。块级标记用于创建段落，标题列表等，内联级主要用于标记文字例如重点，或者链接等。

## 块级标记——主要用于标记文档结构

### 段落

连续的行就是一个段落。空行会将文本分成两个不同的段落。如果只是想要增加一个换行，而不是一个段落，可以在需要换行的地方增加两个空格或者反斜线。

代码：

~~~
这是一个段落。

这是另一个段落。

这个段落包含一个换行，但是不是一个段落哦。我在这里加上两个空格  
我要换行，就是这样。
~~~

效果：

这是一个段落。

这是另一个段落。

这个段落包含一个换行，但是不是一个段落哦。我在这里加上两个空格  
我要换行，就是这样。

### 标题

kramdown 支持 Setext 格式和 atx 格式的标题。这就要求标题前面必须有空行或者是文档的开始（第一行）：

代码：

~~~
一级标题
-----------

二级标题
=========
~~~

效果：

一级标题
-----------

二级标题
=========

以上表示方法，只能表示两级标题，如果需要更多级的标题，可以使用以下格式：


代码：

~~~
# 一级标题

## 二级标题

### 三级标题

##### 四级标题

##### 五级标题

###### 六级标题
~~~

效果：

# 一级标题

## 二级标题

### 三级标题

##### 四级标题

##### 五级标题

###### 六级标题

标题默认会生成对应的 ID 属性，如果设置参数 `auto_ids` 为 `false`，则步生成标题的 ID 属性：

~~~
{::options auto_ids="false" /}

# 没有 ID 的标题
~~~

### 块级引用

使用 `>` 加一个空格作为开始标记则被认为是一个引用。如果后面的行依然使用该标记，则仍然属于该引用。可以在一个引用内部包含另一个块引用。

代码：
~~~
> A sample blockquote.
>
> >Nested blockquotes are
> >also possible.
>
> ## Headers work too
> This is the outer quote again.
~~~
效果：
> A sample blockquote.
>
> >Nested blockquotes are
> >also possible.
>
> ## Headers work too
> This is the outer quote again.

也可以使用 > 标记一个多行的引用作为一个段落，直到一个空行出现为止。例如：

代码：
~~~
> This is a blockquote
continued on this
and this line.

But this is a separate paragraph.
~~~

效果：
> This is a blockquote
continued on this
and this line.

But this is a separate paragraph.

### 代码块

kramdown 支持两种代码块格式。一种使用四个空格或者一个 tab 缩进来标识，另一种使用波浪线作为分割符——这就不用在内容中出现缩进了：

代码：
~~~
这是使用四个空格的代码块。

    Continued here.
~~~
效果：

这是使用四个空格的代码块。

    Continued here.

另一个种风格：

代码：
~~~~
这是另一种风格的代码块。
~~~
这也是一种代码块。
~~~
~~~~

效果：

这是另一种风格的代码块。
~~~
这也是一种代码块。
~~~~

结束的波浪线不能少于开始的波浪线数目才奏效。

下面的代码块指定了使用的语言：

代码：
~~~~
~~~ ruby
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
~~~
~~~~~~~

效果：

~~~ ruby
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
~~~

### 水平线

插入一个水平线很容易：使用多于三个的星号或者连接线或者下划线，中间可以夹杂空格或者 Tab 或者空行都没有关系。

例如：

代码：
~~~
* * *

---

  _  _  _  _

---------------
~~~

效果：

* * *

### 列表

kramdown 支持有序列表和无序列表。有序列表使用一位数字（不一定是按照顺序使用数字，只要是数字即可）表示序号，然后用一个空格将顺序数字和内容分割开，列表内容属于块级标记，所有行使用相同的缩进，例如：

代码：
~~~
1. This is a list item
2. And another item
2. And the third one
   with additional text
~~~
效果：

1. This is a list item
2. And another item
2. And the third one
   with additional text

作为块级引用，回行会被忽略。

代码：
~~~
* A list item
with additional text
~~~
效果：
* A list item
with additional text

如果内容包含块级标记的时候，可以这样：

代码：
~~~
1.  This is a list item

    > with a blockquote

    # And a header

2.  Followed by another item
~~~
效果：
1.  This is a list item

    > with a blockquote

    # And a header

2.  Followed by another item

创建次级列表也很容易：

代码：
~~~
1. Item one
   1. sub item one
   2. sub item two
   3. sub item three
2. Item two
~~~
效果：
1. Item one
   1. sub item one
   2. sub item two
   3. sub item three
2. Item two

在段落后的列表最好要空一行，否则就会跟前面的段落混到一起（目前好像此问题已解决了，挨着也无妨）。

代码：
~~~
This is a paragraph.
1. This is NOT a list.

1. This is a list!
~~~
效果：

This is a paragraph.
1. This is NOT a list.

1. This is a list!

无序列表可以使用星号、加号、连接号（减号）开始，然后加上一个空格，就可以输入内容了。这几种符号混用也可以，不会影响最终的列表：

代码：
~~~
* Item one
+ Item two
- Item three
~~~
效果：

* Item one
+ Item two
- Item three

### 术语列表

术语列表跟普通列表类似，主要用于解释相关术语。术语列表使用普通的段落开始，然后使用一个冒号标明术语解释内容。一个术语可以有几种解释，多个术语也可以使用一个解释。一个段落包含一个术语：

代码：
~~~
term
: definition
: another definition

another term
and another term
: and a definition for the term
~~~
效果：

term
: definition
: another definition

another term
and another term
: and a definition for the term

如果在一个解释前加入一个空行（在术语和解释之间有且只有一个空行），那么该解释将会被一个段落标签包裹：

代码：
~~~
term

: definition
: definition
~~~
效果：

term

: definition
: definition

每个术语可以使用内联标识，每个解释也会被解析为块级标识，例如，你可以在解释中使用任何块级标识。例如下面的解释使用了相同的缩进：

代码：
~~~
This *is* a term

: This will be a para

  > a blockquote

  # A header
~~~
效果：

This *is* a term

: This will be a para

  > a blockquote

  # A header

### 表格

karamdown 可以创建简单的表格。使用管道符号（`|`）开始的行将被认为是表格行。如果管道符号后面紧跟着一个连接线符号（-），这就是一个分割线了。分割线用来分开一个表的表头和主体（亦可以设置表格的对齐方式），也可以用来分割表格的不同部分。如果管道符号后面跟着一个等号（=），下面的表格行旧时表尾的一部分了。

代码：
~~~
| A simple | table |
| with multiple | lines|
~~~

效果：

| A simple | table |
| with multiple | lines|

而代码：

~~~
| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|----
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|=====
| Foot1   | Foot2   | Foot3
{: rules="groups"}
~~~

效果是这样的：

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|----
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|=====
| Foot1   | Foot2   | Foot3
{: rules="groups"}

### HTML 标识

只要在一行的开始地方使用 HTML 标签，那么kramdown 也支持原生 HTML 标签（像div，p，pre等）来标记大段文本块。kramdown 通常不会解析位于 HTML 标签内部的文本。如果设置了 `parse_block_html` 参数值就另当别论了。当该值为 `true` 时，标签内的文本将会被 kramdown 解析和处理：

代码：
~~~
<div style="float: right">
Something that stays right and is not wrapped in a para.
</div>

{::options parse_block_html="true" /}

<div>
This is wrapped in a para.
</div>
<p>
This can contain only *span* level elements.
</p>
~~~

效果：
<div style="float: right">
Something that stays right and is not wrapped in a para.
</div>

{::options parse_block_html="true" /}

<div>
This is wrapped in a para.
</div>
<p>
This can contain only *span* level elements.
</p>

### 块属性

可以给一个块级标识设定属性值。在一个块标识的后面设定 `块的内联属性值` （或者简称为 块的 IAL）。一个块的 IAL 使用大括号包裹，然后是一个冒号，再然后识属性值的设定。例如：

代码：
~~~
> A nice blockquote
{: title="Blockquote title"}
~~~
效果：
> A nice blockquote
{: title="Blockquote title"}

作为经常需要设定标签的 CSS 类，可以使用简写形式：

代码：
~~~
> A nice blockquote
{: .class1 .class2}
~~~
效果：
> A nice blockquote
{: .class1 .class2}

同理，需要设定 ID 可以使用简写形式：

代码：
~~~
> A nice blockquote
{: #with-an-id}
~~~
效果：
> A nice blockquote
{: #with-an-id}

有时在很多标签中需要使用相同的属性，kramdown 可以定义一个 ALD（attribute list defination），然后在 IAL 块中使用该定义。ALD 的定义结构类同 IAL，只不过需要冒号+参考名字+另一个冒号。例如：

代码：
~~~
{:refdef: .c1 #id .c2 title="title"}
paragraph
{: refdef}
~~~
效果：
{:refdef: .c1 #id .c2 title="title"}
paragraph
{: refdef}

IAL 或者 ALD 块的顺序很重要，通常，后定义的属性会覆盖掉前面定义的属性：

代码：
~~~
{:refdef: .c1 #id .c2 title="title"}
paragraph
{: refdef .c3 title="t" #para}
~~~
效果：
{:refdef: .c1 #id .c2 title="title"}
paragraph
{: refdef .c3 title="t" #para}

### 扩展

kramdown 提供一些不太常用却有可能用到的功能。这样当需要该功能的时候就可以直接使用了。例如，可以忽略一些文本的功能（例如注释），或者需要输出一些不需要解析的文本等。例如：

代码：
~~~
This is a paragraph
{::comment}
This is a comment which is
completely ignored.
{:/comment}
... paragraph continues here.

Extensions can also be used
inline {::nomarkdown}**see**{:/}!
~~~
效果：
This is a paragraph
{::comment}
This is a comment which is
completely ignored.
{:/comment}
... paragraph continues here.

Extensions can also be used
inline {::nomarkdown}**see**{:/}!

通过上面的示例可以看出，扩展语法类似于 ALD。只不过标签都以冒号开始。结束标签可以省略为 `{:/}` ，如果标签没有实质内容，可以直接以反斜杠结束，然后加上括号：

代码：
~~~
{::options auto_ids="false" /}

# Header without id
~~~
效果：
{::options auto_ids="false" /}

# Header without id
