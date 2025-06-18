---
layout: post
title:  "Hunspell 文件格式"
date:   2025-06-12 7:30:34 +0800
categories: hunspell
---

## NAME
hunspell - Hunspell 词典和词缀文件格式

## 简介
Hunspell 需要通过两个文件来定义一种语言的拼写检查：一个词典文件——包含单
词和应用的标记，以及一个词缀文件——定义这些标记如何控制拼写。私人定制的
词典文件为非必选项。

### 词典文件
一个词典文件（*.dic）就是一个单词列表，每行一个单词。第一行是词典（不含私人
定制词典）的行数（也就是单词数，此数字用于优化哈希内存大小）。每个单词可包
含一个斜线（"/"）和一个或多个标记，这写标记代表单词的属性，例如后缀。

注：词典单词也可包含斜线，需要通过 `"\/"` 转义。

词典中不仅要添加单词，还有添加一些词组以备拼写检查时推荐使用——诸如常见拼
写错误或者丢失空格等，如下例中 "alot" 和 "inspite"（参见有关常见拼写错误修改
建议的 "REP" 和字段 "ph:"）：

    3
    word
    a lot
    in spite

### 私人定制的词典文件
私人定制的词典文件就是一个简单的单词列表。词前带星号表明该词是禁用词。被斜
线分开的第二个词用来设置词缀。

    foo
    Foo/Simpson
    *bar

此例中，"foo" 和 "Foo" 是私人定制词汇，带有词缀 Simpson (Foo's 等)的 Foo 会被
识别，且 bar 是禁词。

### 示例
词典文件：

    3
    hello
    try/B
    work/AB

标记 B 和 A 定义了词的属性。

词缀文件：

    SET UTF-8
    TRY esianrtolcdugmphbyfvkwzESIANRTOLCDUGMPHBYFVKWZ'

    REP 2
    REP f ph
    REP ph f

    PFX A Y 1
    PFX A 0 re .

    SFX B Y 2
    SFX B 0 ed [^y]
    SFX B y ied y

词缀文件中，定义了前缀 A 和后缀 B。标记 A 定义了一个 `re-` 前缀。类 B 定义了
两个 `-ed` 后缀。第一个 B 后缀在单词的最后一个字符不是 `y` 时可添加。第二个
后缀在单词以 `y` 结尾时添加。

这个词典和词缀的所有组合是："hello", "try", "tried", "work", "worked", "rework",
 "reworked"。

### 词缀文件通用参数项
Hunspell 源码中包含了超过 80 种参数的使用示范。

#### SET 编码
设置字典文件和词词缀文件中单词和词素的字符编码。可能值：UTF-8，ISO8859-1 - 
ISO8859-10，ISO8859-13 - ISO8859-15，KOI8-R，KOI8-U，cp1251，ISCII-DEVANAGARI。

    SET UTF-8

#### FLAG 值
设置标记类型。默认类型是扩展的 ASCII (8-bit) 字符。`UTF-8` 参数设置 UTF-8 编
码的 Unicode 字符标记。`long` 值设置双倍的扩展 ASCII 字符标记类型，`num` 设置
数字标记类型。数字标记从 1 到 65000，在标记字段用逗号隔开。

BUG: UTF-8 标记类型在 ARM 平台不能正常运行。

    FLAG long

#### COMPLEXPREFIXES
设置双重前缀去除(but single suffix stripping) 例如从右到左书写的词法复杂的语言。

#### LANG 语言代码
为 Hunspell 的特定语言功能设置语言代码。用来激活阿塞拜疆文（LANG az）、土耳
其文（LANG tr）和克里米亚鞑靼文（LANG  crh）的特定大小写功能，也并不仅仅是匈
牙利文（LANG hu）的泛义的音节复杂技术规则。

#### IGNORE 字符
设置字典、词缀和输入词的忽略字符。很有用的可选字符，例如阿拉伯文 (harakat) 
或者希伯来文(niqqud) 的变音符号（查看在 Hunspell 发布版本中测试词典的 tests/
ignore.* 文件）。

#### 词缀文件格式

    AF number_of_flag_vector_aliases
    AF flag_vector

Hunspell 可以在词缀规则中用常见数字替代词缀标记（alias compression, see 
makealias tool)。第一个用 alias compression 的示例：

    3
    hello
    try/1
    work/2

AF 词缀定义文件：

    AF 2
    AF A
    AF AB

这等同于下面的词典文件：

    3
    hello
    try/A
    work/AB

查阅源码的 tests/alias* 示例。

注 I：如果词缀文件包含标记参数，在 AF 定义前定义它。

注 II：在 Hunspell 分发时用 makealias 工具压缩 aff 和 dic 文件。

    AM number_of_morphological_aliases
    AM morphological_fields

Hunspell 可以在词最文件中用序数替换词性数据（alias compression）。查阅示例中的
tests/alias*。

### AFFIX 文件中的建议参数项
Hunspell 建议参数可以优化默认的 n-gram （类似在词典单词中基于常见的 1, 2, 3, 
4 个字符长度的字符序列检索）算法的字符替换和删除建议等。建议用 REP 来修正输
入和语言特定问题，因为 REP 建议具有建议的最高级权限。PHONE 主要针对不以发音
为基础的语言系统。

对于简短的常见拼写错误，用 ph: field （见后面）给出最佳建议很重要。

#### KEY 竖线分割的字符
Hunspell 检索和建议有相邻键字符键构成的单词，而不是在字符串中相邻的字构成的
字符串。建议 KEY 参数基于 QWERTY 和 Dvorak 键盘布局：

    KEY qwertyuiop|asdfghjkl|zxcvbnm
    KEY pyfgcrl|aeouidhtns|qjkxbmwvz

使用第一个 QWERTY 布局，Hunspell 对于 "*nide" 会建议 "nude" 和 "node"。一个字
符会有更多的邻键：

    KEY qwertzuop|yxcvbnm|qaw|say|wse|dsx|sy|edr|fdc|dx|rft|gfv|fc|tgz|hgb|gv|zhu|jhn|hb|uji|kjm|jn|iko|lkm

#### TRY 字符
当 Hunspell 通过 TRY 字符发现一个可能的错误输入单词时会建议正确的词形。TRY 
参数大小写敏感。

#### NOSUGGEST flag
带有 NOSUGGEST 标记的单词不会推荐修改词（但仍会接受正确输入）。建议为粗俗和
淫秽词语添加标记
（参见 SUBSTANDARD）。

#### MAXCPDSUGS num
设置由组合规则生成的推荐词的最大值。推荐词的最大值可能大于同样的 1 字符距离
类型。

#### MAXNGRAMSUGS num
设置 n-gram 推荐词最大值。0 值意味着关闭 n-gram 推荐（参见 MAXDIFF）。

#### MAXDIFF [0-10]
为 n-gram 推荐设置相似性因子（5 = 默认值；0  = 最低 n-gram 推荐，但是 min. 1; 
10 = MAXNGRAMSUGS n-gram 推荐）。

#### ONLYMAXDIFF
删除所有错误 n-gram 推荐词（默认模式保留一个，查阅 MAXDIFF）。

#### NOSPLITSUGS
空格关闭推荐词。

#### SUGSWITHDOTS
如果输入词以点结束，启用点推荐。（不适用于 LibreOffice 词典，因为 LibreOffice
有自动启用点推荐机制。）

REP 参数：

    REP 替换定义行数
    REP 替换什么

此表规定首先尝试修改的词汇。第一行 REP 是此表表头表明接下来的多少行是 REP 数据。
通过此表，Hunspell 为典型超过一个字母的错误拼写推荐正确的形式（查阅“ph:”）。
搜索字符串支持正则表达式的边界符（^ 和 $）。例如一个可能的英文处理错误拼写辅音
替换表定义如下：

    REP 5
    REP f ph
    REP ph f
    REP tion$ shun
    REP ^cooccurr co-occurr
    REP ^alot$ a_lot

注 I：这对大多数一个字符的错误拼写很有用，另：用 REP 你可以添加更高级的 TRY 
建议子集（
建议列表以 REP 建议来时）。

注 II：建议分割单词以下划线代替空格：

    REP 1
    REP onetwothree one_two_three

注  III：替换表可通过 CHECKCOMPOUNDREP 参数用于更严格的词组检查。

MAP 参数：

    MAP 映射定义行数
    MAP 相关字符或者括号内的字符序列组成的字符串

我们通过词缀文件的映射表来定义基于语言特点的通常具有一定关联的字符或者字符
串（例如，可以认为在字符集中最接近的）。通过此表，Hunspell 可以猜出单词的正
确形式——通过不断从错字和错词组集合中校正而得到正确形式（参阅 REP）。

例如，可以用映射来对应德语中带元音变音符的 ü 与正常的 u；单词 Frühstück 应当
用带元音变音符的 ü，而不是常规的 u

    MAP 1
    MAP uü

用括号分组字符序列（例如组合的 Unicode 字符）：

    MAP 3
    MAP ß(ss)  (字符序列)
    MAP ﬁ(fi)  (Unicode 连字的 "fi" 兼容字符)
    MAP (ó)o   (组合 Unicode 字符：带底点的 ó)

PHONE 参数：

    PHONE 语音定义总行数
    PHONE 替换内容

PHONE 借鉴了来自于 Aspell 的表驱动的音标转换算法。它对于不以发音为基础的语言
系统是很有用的。你可以添加一个全部的字母转换和其他转化到特定字母序列的规则。
更多细节参阅文档 http://aspell.net/man-html/Phonetic-Code.html。注：多字节 UTF-8 
字符还不能支持括号表达式。破折号表达式可用于字节，但是不支持 UTF-8 字符。

#### WARN 标记
此标记很少词能用到，这些词通常是经常拼写错误的，查阅 Hunspell 命令行的 -r 参数和
FORBIDWARN 选项。

#### FORBIDWARN
使用 WARN 标记不能被拼写检查接受的词使用此参数。

### 组合项
#### BREAK 项
 
    BREAK 定义截断的总行数
    BREAK 符号或者符号序列

定义分词和断词的新断点，用 `^` 和 `$` 删除单词结尾和开始的字符。理由：这对于
使用连接符和字符串的 com‐pounding 很用用（例如，英语和 Ger‐man 中的横线或者
匈牙利文中的多横线）。横线对于 tok‐enization 来说是不好的断点，因为带有横线
的合成词可能会包含无效部分。用 BREAK，Hunspell 可以检查这些合成词，既包括横线
也包括多横线：

    BREAK 2
    BREAK -
    BREAK --    # n-dash

    BREAK 3
    BREAK -
    BREAK ^-
    BREAK -$

Hunspell 通过 BREAK 定义将不接受 "-word" 和 "word-" 形式的词：

    BREAK 1
    BREAK -

关闭默认值：

    BREAK 0

注 II：COMPOUNDRULE 更适合处理横线和其他合成连接符或者字符串。用 BREAK，如果你想要检查词的横线或者其他连接符，且没时间或者不能用 COMPOUNDRULE 精准描述合成规则（COMPOUNDRULE 只处理合成词的最后部分或者后缀）。

注 III：对于使用命令行做带有扩展字符的拼写检查时，设置 WORDCHARS 参数：WORDCHARS
 --- （查阅 tests/break.*）示例

COMPOUNDRULE 格式：

    COMPOUNDRULE 定义的行数
    COMPOUNDRULE compound_pattern

用类正则语法定义定制组合模式。第一个 COMPOUNDRULE 是表头，指定接下来的 COMPOUNDRULE 定义行数。组合模式包括组合 flag，括号，星号和问号元字符。一个 flag 跟着一个 `*` 匹
配带有这个组合 flag 的 0 到多个字符的词。一个 flag 跟着一个 `?` 匹配有该 flag
的 0 或者 1 个字符的词。查阅 tests/compound*.* 示例。

注：OpenOffice.org 的 en_US 词典用 COMPOUNDRULE 做序数识别（1st, 2nd, 11th, 12th, 22nd, 112th, 1000122nd 等）。

注 II：long 数字 flag 类型使用括号 flag：
(1500)*(2000)?

注 III：COMPOUNDRULE flag 运行完全同使用组合机制的 COMPOUNDFLAG、COMPOUNDBEGIN 等组合 flag 不同（在不同的词条用这些 flag）。

##### COMPOUNDMIN num
使用组合的最小长度。默认值是 3 个字母。

##### COMPOUNDFLAG flag

Words  signed  with  COMPOUNDFLAG may be in compound words (except when word shorter
than COMPOUNDMIN). Affixes with COMPOUNDFLAG also  permits  compounding  of  affixed
words.

##### COMPOUNDBEGIN flag
Words  signed  with  COMPOUNDBEGIN (or with a signed affix) may be first elements in
compound words.

##### COMPOUNDLAST flag
Words signed with COMPOUNDLAST (or with a signed affix) may be last elements in com‐
pound words.

##### COMPOUNDMIDDLE flag
Words  signed with COMPOUNDMIDDLE (or with a signed affix) may be middle elements in
compound words.

##### ONLYINCOMPOUND flag
Suffixes signed with ONLYINCOMPOUND flag may be only inside of compounds  (Fuge-ele‐
ments  in  German,  fogemorphemes  in Swedish).  ONLYINCOMPOUND flag works also with
words (see tests/onlyincompound.*).  Note: also valuable to flag  compounding  parts
which are not correct as a word by itself.

##### COMPOUNDPERMITFLAG flag
Prefixes  are allowed at the beginning of compounds, suffixes are allowed at the end
of compounds by default.  Affixes with COMPOUNDPERMITFLAG  may  be  inside  of  com‐
pounds.

##### COMPOUNDFORBIDFLAG flag
Suffixes  with  this  flag forbid compounding of the affixed word.  Dictionary words
with this flag are removed from the beginning and middle of compound words, overrid‐
ing the effect of COMPOUNDPERMITFLAG.

##### COMPOUNDMORESUFFIXES
Allow twofold suffixes within compounds.

##### COMPOUNDROOT flag
COMPOUNDROOT  flag signs the compounds in the dictionary (Now it is used only in the
Hungarian language specific code).

##### COMPOUNDWORDMAX number
Set maximum word count in a compound word. (Default is unlimited.)

##### CHECKCOMPOUNDDUP
Forbid word duplication in compounds (e.g. foofoo).

##### CHECKCOMPOUNDREP
Forbid compounding, if the (usually bad) compound word may be  a  non-compound  word
with a REP fault. Useful for languages with `compound friendly' orthography.

##### CHECKCOMPOUNDCASE
Forbid upper case characters at word boundaries in compounds.

##### CHECKCOMPOUNDTRIPLE
Forbid  compounding, if compound word contains triple repeating letters (e.g. foo|ox
or xo|oof). Bug: missing multi-byte character support in UTF-8 encoding (works  only
for 7-bit ASCII characters).

##### SIMPLIFIEDTRIPLE
Allow  simplified  2-letter forms of the compounds forbidden by CHECKCOMPOUNDTRIPLE.
It's useful  for  Swedish  and  Norwegian  (and  for  the  old  German  orthography:
Schiff|fahrt -> Schiffahrt).

##### CHECKCOMPOUNDPATTERN number_of_checkcompoundpattern_definitions

##### CHECKCOMPOUNDPATTERN endchars[/flag] beginchars[/flag] [replacement]
Forbid  compounding,  if the first word in the compound ends with endchars, and next
word begins with beginchars and (optionally) they have the requested flags.  The op‐
tional replacement parameter allows simplified compound form.

The  special  "endchars"  pattern  0  (zero) limits the rule to the unmodified stems
(stems and stems with zero affixes):

    CHECKCOMPOUNDPATTERN 0/x /y

Note: COMPOUNDMIN doesn't work correctly with the compound word alternation, so it may need
to set COMPOUNDMIN to lower value.

##### FORCEUCASE flag
Last word part of a compound with flag FORCEUCASE forces capitalization of the whole
compound word. Eg. Dutch word "straat" (street) with FORCEUCASE flags  will  allowed
only in capitalized compound forms, according to the Dutch spelling rules for proper
names.

##### COMPOUNDSYLLABLE max_syllable vowels
Need for special compounding rules in Hungarian.  First  parameter  is  the  maximum
syllable number, that may be in a compound, if words in compounds are more than COM‐
POUNDWORDMAX.  Second parameter is the list of vowels (for calculating syllables).

##### SYLLABLENUM flags
匈牙利语中需要的特定组合规则。Need for special compounding rules in Hungarian.

### 用于词缀创建的词缀文件选项
    PFX flag cross_product number
    PFX flag stripping prefix [condition [morphological_fields...]]
    SFX flag cross_product number
    SFX flag stripping suffix [condition [morphological_fields...]]

词缀不管是前缀还是后缀都会通过连接到词根而形成另一个词。我们可以定义任意数
量的词缀类。词缀类用词缀标志标记。词缀类定义的第一行是表头。词缀类表头字段
如下：

    (0) 参数名 (PFX 或 SFX)
    (1) Flag (词缀类的名字)
    (2) Cross product (连接前后缀的权限)。可能值：Y（是）或 N（否）
    (3) 接下来规则的行数。

词缀规则字段：

    (0) 参数名
    (1) Flag
    (2) 从单词的开始（对于前缀规则）或者结尾（对于后缀规则）删掉字符
    (3) 词缀（可连续使用类 flag，用斜杠分开）
    (4) 条件。零剥离或者词缀通过零来标记。零条件通过 `.` 标识。条件是简洁的
        类正则表达式模式，在应用词缀变化之前必须通过筛选。（点表示任意数量的字
        符，括号中的字符表示该子字符集中的任意字符。横线没有任何特殊意义，但是
        抑扬音符（`^`）却表示挨着的括号字符集的反选。）
    (5) 可选词性字段，由空格或者 TAB 分开。

### 词缀文件其他参数
#### CIRCUMFIX flag
Affixes signed with CIRCUMFIX flag may be on a word when this word also has a prefix
with CIRCUMFIX flag and vice versa (see circumfix.* test files in the source distri‐
bution).

#### FORBIDDENWORD flag
This  flag  signs forbidden word form. Because affixed forms are also forbidden, we
can subtract a subset from set of the accepted affixed and  compound  words.   Note:
usefull to forbid erroneous words, generated by the compounding mechanism.

#### FULLSTRIP
      With  FULLSTRIP, affix rules can strip full words, not only one less characters, be‐
      fore adding the affixes, see fullstrip.* test files  in  the  source  distribution).
      Note: conditions may be word length without FULLSTRIP, too.

#### KEEPCASE flag
      Forbid  uppercased and capitalized forms of words signed with KEEPCASE flags. Useful
      for special orthographies (measurements and currency often keep their case in upper‐
      cased  texts) and writing systems (e.g. keeping lower case of IPA characters).  Also
      valuable for words erroneously written in the wrong case.

      Note: With CHECKSHARPS declaration, words with sharp s and KEEPCASE flag may be cap‐
      italized  and  uppercased, but uppercased forms of these words may not contain sharp
      s, only SS. See germancompounding example in the tests  directory  of  the  Hunspell
      distribution.

#### ICONV number_of_ICONV_definitions

#### ICONV pattern pattern2
      Define input conversion table.  Note: useful to convert one type of quote to another
      one, or change ligature.

#### OCONV number_of_OCONV_definitions

#### OCONV pattern pattern2
      Define output conversion table.

#### LEMMA_PRESENT flag
      Deprecated. Use "st:" field instead of LEMMA_PRESENT.

#### NEEDAFFIX flag
      This flag signs virtual stems in the dictionary, words only valid when affixed.  Ex‐
      cept,  if  the  dictionary word has a homonym or a zero affix.  NEEDAFFIX works also
      with prefixes and prefix + suffix combinations (see tests/needaffix5.*).

#### PSEUDOROOT flag
      Deprecated. (Former name of the NEEDAFFIX option.)

#### SUBSTANDARD flag
      SUBSTANDARD flag signs affix rules and dictionary words  (allomorphs)  not  used  in
      morphological generation and root words removed from suggestion. See also NOSUGGEST.

#### WORDCHARS characters
      WORDCHARS  extends tokenizer of Hunspell command line interface with additional word
      character. For example, dot, dash, n-dash, numbers, percent sign are word  character
      in Hungarian.

#### CHECKSHARPS
      SS letter pair in uppercased (German) words may be upper case sharp s (ß).  Hunspell
      can handle this special casing with the CHECKSHARPS declaration (see  also  KEEPCASE
      flag and tests/germancompounding example) in both spelling and suggestion.

### 词法分析
Hunspell 的词典条目和词缀规则可以使用空格或者 TAB 分开以 3 个字符（两个字母
和一个冒号）词性描述字段：

    word/flags po:noun is:nom

示例：我们定义一个简单的带有词性信息的资源，一个派生词词缀（ds:）和一个词
类（po:）：

词缀文件：

    SFX X Y 1
    SFX X 0 able . ds:able

词典文件：

    drink/X po:verb

测试文件：

    drink
    drinkable

测试：
~~~ shell
$ analyze test.aff test.dic test.txt
> drink
analyze(drink) = po:verb
stem(drink) = po:verb
> drinkable
analyze(drinkable) = po:verb ds:able
stem(drinkable) = drinkable
~~~

可以看到示例中 analyzer 连接了词条中的词性字段。

### 数据字段选项

默认词法和其他 ID（用于建议，词干提取和词发生成）：

ph：基于发音的最佳建议，例如与特定词法或者发音相关的误拼。处理误拼最好的方式
就是通过添加 ph: 字段将误拼最严重的单词添加到字典中以纠正。

示例：

    Wednesday ph:wendsay ph:wensday
    Marseille ph:maarsayl

Hunspell 添加所有的 ph: 音译词到 REP 表中，这些音译词将会优先纠正误拼词。

前面的例词等价于下面的 REP 定义：

    REP 6
    REP wendsay Wednesday
    REP Wendsay Wednesday
    REP wensday Wednesday
    REP Wensday Wednesday
    REP maarsayl Marseille
    REP Maarsayl Marseille

ph: 模式下单词结尾的星号表示剥离终止符，等价于 REP 从模式到单词的规则：

    pretty ph:prity*

会导致

    REP 1
    REP prit prett

REP 规则，结果就是下面的纠正建议

    *prity -> pretty
    *pritier -> prettier
    *pritiest -> prettiest

此外，ph: 字段可以处理超过 2 个词的建议，也与同样误拼的建议不同：

    do not know ph:dunno
    don't know ph:dunno

结果

    *dunno -> do not know, don't know

注：如果需要，ph: 用法同 n-gram。

ASCII 箭头 "->" 在 ph: 模式中意味着一个 REP 规则（查阅 REP），在字典条目中创
建任意的取代规则：

    happy/B ph:hepy ph:hepi->happi

结果

    *hepy -> happy
    *hepiest -> happiest

st:词干（Stem）。选项：默认词干提取是字典条目用于词性分析的。词干提取字段
field is useful for virtual stems (dictionary words with NEEDAFFIX flag) and morpho‐
logical exceptions instead of new, single used morphological rules.

    feet  st:foot  is:plural
    mice  st:mouse is:plural
    teeth st:tooth is:plural

有多个词干的词形需要多个词典条目：

    lay po:verb st:lie is:past_2
    lay po:verb is:present
    lay po:noun

al: 变体（Allomorph(s)）。一个词典条目是多个变体的词干。词性生成于词干、变体和词缀。

    sing al:sang al:sung
    sang st:sing
    sung st:sing

po: 词类（Part of speech category）。

ds: 派生后缀（Derivational suffix(es)）。词干提取不会删除派生后缀。词形生成依
赖于后缀顺序。

词缀规则中：

    SFX Y Y 1
    SFX Y 0 ly . ds:ly_adj

词典中：

    ably st:able ds:ly_adj
    able al:ably

is: 屈折后缀（Inflectional suffix(es)）。所有屈折后缀在词干提取中都会被删除。
词形生成依赖于词缀顺序。

    feet st:foot is:plural

ts: 终结后缀（Terminal  suffix(es)）。终结后缀字段是屈折后缀字段通过附加后缀（非终结）“再次删除”掉。

Useful for zero morphemes and affixes removed by splitting rules.

    work/D ts:present

    SFX D Y 2
    SFX D   0 ed . is:past_1
    SFX D   0 ed . is:past_2

Typical example of the terminal suffix is the zero morpheme of the nominative case.

sp:    Surface prefix. Temporary solution for adding prefixes to the  stems  and  generated
word forms. See tests/morph.* example.

pa:    Parts of the compound words. Output fields of morphological analysis for stemming.

dp:    Planned: derivational prefix.

ip:    Planned: inflectional prefix.

tp:    Planned: terminal prefix.

### 双重后缀移除
Ispell 的原始算法只移除一个后缀。Hunspell 还可以移除另一个（或者一个
 COMPLEXPREFIXES 模式中的前缀）。

双重后最移除对于处理数量庞大的后缀黏着语言而言是一个意义重大的提升。

下例中第二个后缀 `s` （词缀类 Y）会接在后缀类 `able` 之后：

    SFX Y Y 1
    SFX Y 0 s .

    SFX X Y 1
    SFX X 0 able/Y .

词典文件：

    drink/X

测试文件：

    drink
    drinkable
    drinkables

测试：

~~~ shell
    $ hunspell -m -d test < test.txt
    drink st:drink
    drinkable st:drink fl:X
    drinkables st:drink fl:X fl:Y
~~~

理论上，与 Hunspell 实现相比，双重词缀移除只需要后缀规则数的平方根，在我们的
实践中，我们可以通过双重后缀移除简化匈牙利语的屈折语形态。

### 扩展词缀类
Hunspell 可以处理超过 65000 种词缀类。在词典和词缀种有三种新语法给定 flag。

FLAG long 命令设置 2-字符 flag：

                FLAG long
                SFX Y1 Y 1
                SFX Y1 0 s 1

词典记录中标记有 Y1, Z3, F? flags:

                foo/Y1Z3F?

FLAG num 命令设置逗号分割的数字 flag：

                FLAG num
                SFX 65000 Y 1
                SFX 65000 0 s 1

词典示例：

                foo/65000,12,2756

第三个是 Unicode 字符 flag。

### 多义词
Hunspell 词典中用重复元素表示多义词：

               work/A    po:verb
               work/B    po:noun

词缀文件：

               SFX A Y 1
               SFX A 0 s . sf:sg3

               SFX B Y 1
               SFX B 0 s . is:plur

测试文件：

               works

测试：

               $ hunspell -d test -m <testwords
               work st:work po:verb is:sg3
               work st:work po:noun is:plur

此功能也为禁用非法 prefix/suffix 组合提供了一种方式。

### 前缀后缀依赖
An  interesting  side-effect  of multi-step stripping is, that the appropriate treatment of
circumfixes now comes for free.  For instance, in Hungarian, superlatives are formed by si‐
multaneous  prefixation  of  leg-  and suffixation of -bb to the adjective base.  A problem
with the one-level architecture is that there is no way to render lexical licensing of par‐
ticular  prefixes and suffixes interdependent, and therefore incorrect forms are recognized
as valid, i.e. *legvén = leg + vén `old'. Until the introduction  of  clusters,  a  special
treatment  of  the  superlative  had to be hardwired in the earlier HunSpell code. This may
have been legitimate for a single case, but in fact prefix--suffix dependences are  ubiqui‐
tous in category-changing derivational patterns (cf. English payable, non-payable but *non-
pay or drinkable, undrinkable but *undrink). In simple words, here, the prefix un-  is  le‐
gitimate  only  if the base drink is suffixed with -able. If both these patters are handled
by on-line affix rules and affix rules are checked against the base only, there is  no  way
to express this dependency and the system will necessarily over- or undergenerate.

In next example, suffix class R have got a prefix `continuation' class (class P).

              PFX P Y 1
              PFX P   0 un . [prefix_un]+

              SFX S Y 1
              SFX S   0 s . +PL

              SFX Q Y 1
              SFX Q   0 s . +3SGV

              SFX R Y 1
              SFX R   0 able/PS . +DER_V_ADJ_ABLE

Dictionary:

              2
              drink/RQ  [verb]
              drink/S   [noun]

Morphological analysis:

              > drink
              drink[verb]
              drink[noun]
              > drinks
              drink[verb]+3SGV
              drink[noun]+PL
              > drinkable
              drink[verb]+DER_V_ADJ_ABLE
              > drinkables
              drink[verb]+DER_V_ADJ_ABLE+PL
              > undrinkable
              [prefix_un]+drink[verb]+DER_V_ADJ_ABLE
              > undrinkables
              [prefix_un]+drink[verb]+DER_V_ADJ_ABLE+PL
              > undrink
              Unknown word.
              > undrinks
              Unknown word.

### 双向词缀
Conditional affixes implemented by a continuation class are not enough for circumfixes, be‐
cause a circumfix is one affix in morphology. We also need  CIRCUMFIX  option  for  correct
morphological analysis.

              # circumfixes: ~ obligate prefix/suffix combinations
              # superlative in Hungarian: leg- (prefix) AND -bb (suffix)
              # nagy, nagyobb, legnagyobb, legeslegnagyobb
              # (great, greater, greatest, most greatest)

              CIRCUMFIX X

              PFX A Y 1
              PFX A 0 leg/X .

              PFX B Y 1
              PFX B 0 legesleg/X .

              SFX C Y 3
              SFX C 0 obb . +COMPARATIVE
              SFX C 0 obb/AX . +SUPERLATIVE
              SFX C 0 obb/BX . +SUPERSUPERLATIVE

Dictionary:

              1
              nagy/C    [MN]

Analysis:

              > nagy
              nagy[MN]
              > nagyobb
              nagy[MN]+COMPARATIVE
              > legnagyobb
              nagy[MN]+SUPERLATIVE
              > legeslegnagyobb
              nagy[MN]+SUPERSUPERLATIVE

### 组合
Allowing free compounding yields decrease in precision of recognition, not to mention stem‐
ming and morphological analysis.  Although lexical switches are introduced to license  com‐
pounding of bases by Ispell, this proves not to be restrictive enough. For example:

              # affix file
              COMPOUNDFLAG X

              2
              foo/X
              bar/X

With this resource, foobar and barfoo also are accepted words.

This has been improved upon with the introduction of direction-sensitive compounding, i.e.,
lexical features can specify separately whether a base can occur as leftmost  or  rightmost
constituent  in  compounds.   This,  however, is still insufficient to handle the intricate
patterns of compounding, not to mention idiosyncratic (and language specific) norms of  hy‐
phenation.

The  Hunspell  algorithm  currently  allows  any affixed form of words, which are lexically
marked as potential members of compounds. Hunspell improved this, and  its  recursive  com‐
pound  checking  rules makes it possible to implement the intricate spelling conventions of
Hungarian compounds. For example, using  COMPOUNDWORDMAX,  COMPOUNDSYLLABLE,  COMPOUNDROOT,
SYLLABLENUM  options  can  be  set the noteworthy Hungarian `6-3' rule.  Further example in
Hungarian, derivate suffixes often modify compounding properties. Hunspell allows the  com‐
pounding  flags  on  the  affixes,  and there are two special flags (COMPOUNDPERMITFLAG and
(COMPOUNDFORBIDFLAG) to permit or prohibit compounding of the derivations.

Suffixes with this flag forbid compounding of the affixed word.

We also need several Hunspell features for handling German compounding:

              # German compounding

              # set language to handle special casing of German sharp s

              LANG de_DE

              # compound flags

              COMPOUNDBEGIN U
              COMPOUNDMIDDLE V
              COMPOUNDEND W

              # Prefixes are allowed at the beginning of compounds,
              # suffixes are allowed at the end of compounds by default:
              # (prefix)?(root)+(affix)?
              # Affixes with COMPOUNDPERMITFLAG may be inside of compounds.
              COMPOUNDPERMITFLAG P

              # for German fogemorphemes (Fuge-element)
              # Hint: ONLYINCOMPOUND is not required everywhere, but the
              # checking will be a little faster with it.

              ONLYINCOMPOUND X

              # forbid uppercase characters at compound word bounds
              CHECKCOMPOUNDCASE

              # for handling Fuge-elements with dashes (Arbeits-)
              # dash will be a special word

              COMPOUNDMIN 1
              WORDCHARS -

              # compound settings and fogemorpheme for `Arbeit'

              SFX A Y 3
              SFX A 0 s/UPX .
              SFX A 0 s/VPDX .
              SFX A 0 0/WXD .

              SFX B Y 2
              SFX B 0 0/UPX .
              SFX B 0 0/VWXDP .

              # a suffix for `Computer'

              SFX C Y 1
              SFX C 0 n/WD .

              # for forbid exceptions (*Arbeitsnehmer)

              FORBIDDENWORD Z

              # dash prefix for compounds with dash (Arbeits-Computer)

              PFX - Y 1
              PFX - 0 -/P .

              # decapitalizing prefix
              # circumfix for positioning in compounds

              PFX D Y 29
              PFX D A a/PX A
              PFX D Ä ä/PX Ä
               .
               .
              PFX D Y y/PX Y
              PFX D Z z/PX Z

Example dictionary:

              4
              Arbeit/A-
              Computer/BC-
              -/W
              Arbeitsnehmer/Z

Accepted compound compound words with the previous resource:

              Computer
              Computern
              Arbeit
              Arbeits-
              Computerarbeit
              Computerarbeits-
              Arbeitscomputer
        Circumfix      Arbeitscomputern
              Computerarbeitscomputer
              Computerarbeitscomputern
              Arbeitscomputerarbeit
              Computerarbeits-Computer
              Computerarbeits-Computern

Not accepted compoundings:

              computer
              arbeit
              Arbeits
              arbeits
              ComputerArbeit
              ComputerArbeits
              Arbeitcomputer
              ArbeitsComputer
              Computerarbeitcomputer
              ComputerArbeitcomputer
              ComputerArbeitscomputer
              Arbeitscomputerarbeits
              Computerarbeits-computer
        Circumfix      Arbeitsnehmer

This solution is still not ideal, however, and will be replaced  by  a  pattern-based  com‐
pound-checking  algorithm  which is closely integrated with input buffer tokenization. Pat‐
terns describing compounds come as a separate input resource that can refer  to  high-level
properties of constituent parts (e.g. the number of syllables, affix flags, and containment
of hyphens). The patterns are matched against potential segmentations of compounds  to  as‐
sess wellformedness.

### Unicode 字符编码
Ispell 和 Myspell 都使用 8-bit ASCII 字符编码，这在需要扩展时就成了最大的不足
之处。尽管一种语言例如匈牙利语有标准的 ASCII 字符集（ISO  8859-2），也不能完
全基于匈牙利语的正字法实现全部的规则。例如，这个字符集中丢失了 '--' 符号
（n-dash）从而忽略了这是分割括号子句的官方符号，而且做为特殊的“大”横线连接
合成词的作用。

MySpell 有 8-bit 编码表，但是有些语言没有标准的 8-bit 编码，例如一些非洲语言
是非拉丁或者拉丁扩展字符。

Similarly, using the original spelling of certain foreign names like Ångström or Molière is
encouraged  by  the Hungarian spelling norm, and, since characters 'Å' and 'è' are not part
of ISO 8859-2, when they combine with inflections containing characters only in  ISO 8859-2
(like  elative  -ből,  allative  -től  or delative -ről with double acute), these result in
words (like Ångströmről or Molière-től.) that can not be encoded using any single ASCII en‐
coding scheme.

有关 8-bit ASCII 编码的问题被 Unicode 的提案认识到。很明显，当涉及到真正的多
语言应用程序时，编码独立当然要比效率有优势。Hunspel 实现了 Unicode 处理和内
存的高效管理。对于非 UTF-8 字符编码 Hunspell 用原始的 8-bit 字符串处理。对于
 UTF-8 编码，词缀和词都以 UTF-8 编码，分析和处理多数 UTF-8 编码时，以及条件
检查和推荐选词会转为 UTF-16 编码。Unicode 文本分析和拼写检查有一个最少（0-20%）
时间消耗和最少或者合理的内存消耗依赖来自语言（语言的 UTF-8 编码和词缀）。

### Aspell 字典转换
Aspell 字典很容易转换到 hunspell。转换步骤：

辞典 (xx.cwl -> xx.wl):

~~~ shell
preunzip xx.cwl
wc -l < xx.wl > xx.dic
cat xx.wl >> xx.dic
~~~

词缀文件

如果有词缀文件，复制：
~~~ shell
cp xx_affix.dat xx.aff
~~~
如果没有，用相应的字符编码创建（查看 xx.dat）
~~~ shell
echo "SET ISO8859-x" > xx.aff
# 或者
echo "SET UTF-8" > xx.aff
~~~

为字典的字符频率设置单词编辑距离建议添加 TRY 项很有用：

~~~ shell
echo "TRY qwertzuiopasdfghjklyxcvbnmQWERTZUIOPASDFGHJKLYXCVBNM" >>xx.aff
~~~
参阅
    hunspell (1), ispell (1), ispell (4)
