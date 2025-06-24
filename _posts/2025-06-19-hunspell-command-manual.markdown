---
layout: post
title:  "Hunspell 命令手册"
date:   2025-06-19 7:30:34 +0800
categories: hunspell
---

## NAME
hunspell - 拼写检查，词干提取和词形分析软件

## SYNOPSIS
    hunspell  [-1aDGHhLlmnOrstvwX]  [--check-url]  [--check-apostrophe] [-d
    dict[,dict2,...]]  [--help]  [-i  enc]  [-p  dict]  [-vv]   [--version]
    [text/OpenDocument/TeX/LaTeX/HTML/SGML/XML/nroff/troff file(s)]

## 简介

Hunspell 是继 Ispell 程序之后最流行的软件。最常见的用法是`hunspell`
或者`hunspell 文件名`。没有文件名参数时，hunspell 会检测标准输入。
分两行输入“cat”和“exsample”，结果会输出一个星号（意思是“cat”是
一个正确的词）和带有纠正词的一行：

~~~shell
$ hunspell -d en_US
Hunspell 1.2.3
*
& exsample 4 0: example, examples, ex sample, ex-sample
~~~

纠正词带有 `*`、`+` 或 `-`，未识别词带有 `#` 或者在输出行带有 `&`（见后面）。
（在 Unix/Linux 上通过 <kbd>Ctrl + d</kbd> 关闭，在 Windows 上通过 Ctrl + 
Z，再回车（Enter）或者 Ctrl + C 关闭）。

如果有文件名参数，Hunspell 会在屏幕顶端显示每一个词典里没有的词，而且允许你
修改。如果词典里有“邻词错误”，那么也会显示备选词列表。最终，包含有误拼词
的行和误拼词都打印在屏幕顶部。如果你的屏幕支持反向显示视频，则单词可高亮。
你可以选择替换单词或者从建议单词中选择对应单词。命令中单字符参数如下（忽略
示例）：

    R      完全取代误拼词。

    Space  本次接受该词。

    A      本次 Hunspell 会话中都接受该词。

    I      接受该词，并在文件中大写，更新定制词典。

    U      接受该词，在定制词典中添加一个非大写版本词（实际上都小写）。

    S      询问一个词干和一个模型词，且将他们存入定制词典。词干与词缀模型的
           组合将会接受。

    0-n    用一个建议词替换。

    X      写入文件剩余部分，忽略误拼，开始检查下一个文件。

    Q      立即退出，保持文件未更改状态。

    ^Z     暂停 Hunspell。

    ?      给出帮助屏幕。

## 选项
    -1    只检测每行的第一个字段（分割符 = TAB 空格）。

    -a    这个 -a 选项试图通过管道使用其他程序。在此模式下，Hunspell 打
          印一行版鉴定信息，然后开始读取输入行。对于每行输入，都做为单
          独行写入标准输出行，其中的每个单词都会进行拼写检查。如果单词
          在主词典或者定制词典里，那么该行将只有一个 '*'。如果发现单词
          通过修复可以出现在词典，那么该行包含一个 '+'，一个空格和词根。
          如果该词通过组合格式（连接两个词）可以出现在词典，那么行里会
          包含一个 '-'。如果单词不在词典，但是可以通过邻键错误，那么行
          内就包含一个 '&'，一个空格，误拼词，一个空格，误拼数量，行开
          始和误拼词之间的字符数，一个冒号，又一个空格和一列由逗号和空
          格隔开的邻键词。

          Also, each near miss or guess is capitalized the same as the in‐
          put  word  unless  such capitalization is illegal; in the latter
          case each near miss is capitalized correctly  according  to  the
          dictionary.

          Finally,  if  the  word  does  not appear in the dictionary, and
          there are no near misses, then the line contains a '#', a space,
          the  misspelled word, a space, and the character offset from the
          beginning of the line.  Each sentence of text  input  is  termi‐
          nated  with  an  additional blank line, indicating that hunspell
          has completed processing the input line.

          These output lines can be summarized as follows:

          OK:    *

          Root:  + <root>

          Compound:
                 -

          Miss:  & <original> <count> <offset>: <miss>, <miss>, ...

          None:  # <original> <offset>

          For example, a dummy dictionary  containing  the  words  "fray",
          "Frey",  "fry",  and  "refried"  might produce the following re‐
          sponse to the command "echo 'frqy refries | hunspell -a":
          (#) Hunspell 0.4.1 (beta), 2005-05-26
          & frqy 3 0: fray, Frey, fry
          & refries 1 5: refried

          This mode is also suitable for interactive use when you want  to
          figure  out  the  spelling of a single word (but this is the de‐
          fault behavior of hunspell without -a, too).

          When in the -a mode, hunspell will also accept lines  of  single
          words  prefixed  with  any of '*', '&', '@', '+', '-', '~', '#',
          '!', '%', '`', or '^'.  A line starting with '*' tells  hunspell
          to  insert the word into the user's dictionary (similar to the I
          command).  A line starting with '&' tells hunspell to insert  an
          all-lowercase  version  of  the  word into the user's dictionary
          (similar to the U command).  A line  starting  with  '@'  causes
          hunspell  to  accept  this  word in the future (similar to the A
          command).  A line starting with '+', followed immediately by tex
          or nroff will cause hunspell to parse future input according the
          syntax of that formatter.  A line consisting  solely  of  a  '+'
          will place hunspell in TeX/LaTeX mode (similar to the -t option)
          and '-' returns hunspell to nroff/troff mode (but these commands
          are  obsolete).   However,  the  string  character  type  is not
          changed; the '~' command must be used to do this.  A line start‐
          ing with '~' causes hunspell to set internal parameters (in par‐
          ticular, the default string character type) based on  the  file‐
          name  given  in  the rest of the line.  (A file suffix is suffi‐
          cient, but the period must be included.  Instead of a file  name
          or  suffix, a unique name, as listed in the language affix file,
          may be  specified.)   However,  the  formatter  parsing  is  not
          changed;   the '+' command must be used to change the formatter.
          A line prefixed with '#' will cause the personal  dictionary  to
          be saved.  A line prefixed with '!' will turn on terse mode (see
          below), and a line prefixed with '%'  will  return  hunspell  to
          normal  (non-terse) mode.  A line prefixed with '`' will turn on
          verbose-correction mode (see below); this mode can only be  dis‐
          abled by turning on terse mode with '%'.

          Any  input  following  the prefix characters '+', '-', '#', '!',
          '%', or '`' is ignored, as is any input following  the  filename
          on  a '~' line.  To allow spell-checking of lines beginning with
          these characters, a line starting with '^'  has  that  character
          removed  before  it is passed to the spell-checking code.  It is
          recommended that programmatic interfaces prefix every data  line
          with  an uparrow to protect themselves against future changes in
          hunspell.

          To summarize these:

          *      Add to personal dictionary

          @      Accept word, but leave out of dictionary

          #      Save current personal dictionary

          ~      Set parameters based on filename

          +      Enter TeX mode

          -      Exit TeX mode

          !      Enter terse mode

          %      Exit terse mode

          `      Enter verbose-correction mode

          ^      Spell-check rest of line

          In terse mode, hunspell will not print lines beginning with '*',
          '+', or '-', all of which indicate correct words.  This signifi‐
          cantly improves running speed when the driving program is  going
          to ignore correct words anyway.

          In  verbose-correction mode, hunspell includes the original word
          immediately after the indicator character in output lines begin‐
          ning  with  '*',  '+', and '-', which simplifies interaction for
          some programs.

    --check-apostrophe
          Check and force Unicode apostrophes  (U+2019),  if  one  of  the
          ASCII  or  Unicode apostrophes is specified by the spelling dic‐
          tionary, as a word character (see WORDCHARS, ICONV and OCONV  in
          hunspell(5)).

    --check-url
          检查 URL，电子邮箱地址和词典路径。

    -D     显示探测到的已经加载的词典和检索途径以及可提供的词典。

    -d dict,dict2,...
          通过文件名（可能有路径）设置词典。语法示例：

    -d en_US,en_geo,en_med,de_DE,de_med

    en_US 和 de_DE 是词典名，它们由 `aff` 和 `dic` 文件组成。
    套件：en_US.aff，en_US.dic 和 de_DE.aff，de_DE.dic。En_geo，en_med，
    de_med 是特殊词典：不带词缀文件的词典。特殊词典是基础词典的扩展，通常
    都是特殊术语（医学、法学等）词典。特殊词典没有命名约定，只有“.dic”
    扩展：没有词缀文件的词典will be an extension of the preceding base dic‐
    tionary (right order of the parameter list needs for good suggestions).
    First item of -d parameter list must be a base dictionary.

    -G     只打印正确的词或者行。

    -H     输入文件为 SGML/HTML 格式。

    -h, --help
          简短帮助。

    -i enc 设置输入编码。

    -L     打印误拼词行。

    -l     "list" 选项用于生成一列基于标准输入的误拼词。

    -m     分析标准输入文本的词（参阅 hunspell(5) 关于形态学分析）。没有
            词典的形态学数据，面向词典开发的词形的词缀等。

    -n     输入文件为 nroff/troff 格式。

    -O     输入文件为 OpenDocument (ODF or Flat ODF) 格式。如果没有安装
          unzip 程序，则在使用此选项前安装。

    -P password
          为加密词典设置密码。

    -p dict
          设置定制词典路径。默认词典依赖于本地设置。搜索下面环境变量：
           LC_ALL、LC_MESSAGES 和 LANG。如果未设置这些环境变量则默认定
           制词典在 $HOME/.hunspell_default。

          设置 -d 或者 DICTIONARY 环境变量，定制词典将位于
          $HOME/.hunspell_dicname

    -r    罕见词警告，可能预示着潜在的拼写错误。

    -s    提取输入文本（参阅 hunspell(5) 有关词干提取）的词干。它依赖于
          词典数据。

    -t     输入文件为 TeX 或者 LaTeX 格式。

    -v, --version
          打印版本号。

    -vv    打印 ispell(1) 兼容版本号。

    -w     从一 词/行 输入开始打印误拼词 (= lines)。 

    -X     输入文件为 XML 格式。

## 示例
    hunspell example.html
          用默认词典对 HTML 文件进行互动式拼写检查。

    hunspell -d en_US example.html
          用 en_US 词典对 HTML 文件进行互动式拼写检查。

    hunspell -d en_US,en_US_med medical.txt
          用多个词典进行互动式拼写检查。

    hunspell *.odt
          对 ODF 文档进行互动式拼写检查。

    hunspell -l *.odt
          列出 ODF 文档的错词。

    hunspell -l *.odt | sort | uniq >unrecognized
          保存 ODF 文档(filtering duplications)的未识别词。

    hunspell -p unrecognized_but_good *.odt
          互动式检查 ODF 文档，用前一种保存和和减少词列表做为定制词典，
          来加速拼写检查。

    ENVIRONMENT

    DICTIONARY
          同 -d。

    DICPATH
          词典路径。

    WORDLIST
          等效于 -p。

## 文件
默认词典依赖本地设置。软件会先搜索下面的环境变量：LC_ALL、LC_MESSAGES 和 LANG。
如果本地没有设置则会使用下面的设置：

`/usr/share/myspell/default.aff` # 默认词缀文件路径，查阅 hunspell(5)。

`/usr/share/myspell/default.dic` #默认词典文件路径。查阅 hunspell(5)。

`$HOME/.hunspell_default`  #默认个人词典路径。

参阅 hunspell (3)，hunspell(5)

作者：László Németh

本手册基于 Ispell 手册。参阅 ispell(1).
