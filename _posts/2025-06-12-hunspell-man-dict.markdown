---
layout: post
title:  "Hunspell 文件格式"
date:   2025-06-12 7:30:34 +0800
categories: hunspell
---

## NAME
hunspell - Hunspell 词典和词缀文件格式

## 简介
Hunspell(1) Hunspell 需要通过两个文件来定义一种语言的拼写检查：一个词典文件——包含单
词和应用的标记，以及一个词缀文件——定义这些标记如何控制拼写。个人词典文件为可选项。

### 词典文件
一个词典文件（*.dic）包含一个单词表，每个单词一行。第一行是词典（不含个人词典）的单词
数（用于优化哈希内存大小）。每个单词可包含一个斜线（"/"）和一个或多个标记，这写标记代
表单词的属性，例如后缀。

注：词典单词也可包含斜线，需要通过 "\/" 转义。

词典中不仅要添加单词，还有添加一些词组以备拼写推荐使用——诸如常见拼写错误或者丢失空
格等，如下例中 "alot" 和 "inspite"（参见有关常见拼写错误修改建议的 "REP" 和字段 "ph:"）：

    3
    word
    a lot
    in spite

### 个人词典文件
个人词典文件就是一个简单的单词列表。单词前带星号表明该词禁用。被斜线分割的第二个词设
置词缀。

      foo
      Foo/Simpson
      *bar

此例中，"foo" 和 "Foo" 是个人词汇，带有词缀 Simpson (Foo's 等)的 Foo 会被识别，且 bar
是禁词。

### 短示例
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

词缀文件中，定义了前缀 A 和后缀 B。标记 A 定义了一个 `re-' 前缀。类 B 定义了两个 `-ed'
后缀。第一个 B 后缀在单词的最后一个字符不是 `y' 时可添加。第二个后缀在单词以 `y' 结尾
时添加。

这个词典和词缀的所有组合是："hello", "try", "tried", "work", "worked", "rework", "reworked"。

### 词缀文件通用选项
Hunspell 源码中包含了超过 80 中参数的使用示范。

#### SET encoding
设置字典文件和词词缀文件中单词和词素的字符编码。可能值：UTF-8，ISO8859-1 - ISO8859-10，
ISO8859-13 - ISO8859-15，KOI8-R，KOI8-U，cp1251，ISCII-DEVANAGARI。

    SET UTF-8

#### FLAG value
设置标记类型。默认类型是扩展的 ASCII (8-bit) 字符。`UTF-8' 参数设置 UTF-8 编码的 Unicode
字符标记。`long' 值设置双倍的扩展 ASCII 字符标记类型，`num' 设置数字标记类型。数字标记
从 1 到 65000，在标记字段用逗号隔开。
BUG: UTF-8 标记类型在 ARM 平台不能正常运行。

    FLAG long

#### COMPLEXPREFIXES
Set twofold prefix stripping (but single suffix stripping) eg.  for  morphologically
complex languages with right-to-left writing system.

#### LANG langcode
为 Hunspell 的特定语言功能设置语言代码。用来激活阿塞拜疆文（LANG az）、土耳其文（LANG tr）和
克里米亚鞑靼文（LANG  crh）的特定大小写功能，也并不仅仅是匈牙利文（LANG hu）的泛义的音节复杂
技术规则。

#### IGNORE characters
设置字典、词缀和输入词的忽略字符。很有用的可选字符，例如阿拉伯文 (harakat) 或者希伯来文
(niqqud) 的变音符号（查看在 Hunspell 发布版本中测试词典的 tests/ignore.* 文件）。

    AF number_of_flag_vector_aliases
    AF flag_vector

Hunspell 可以在词缀规则中用常见数字替代词缀标记（alias compression, see makealias tool)。第一个
用 alias compression 的示例：

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

### AFFIX 文件中的建议选项
Hunspell 建议参数可以优化默认的 n-gram （类似在词典单词中基于常见的 1, 2, 3, 4 个字符长度的字符
序列检索）算法的字符替换和删除建议等。建议用 REP 来修正输入和语言特定问题，因为 REP 建议具有建
议的最高级权限。PHONE 主要针对不以发音为基础的语言系统。

对于简短的常见拼写错误，用 ph: field （见后面）给出最佳建议很重要。

#### KEY characters_separated_by_vertical_line_optionally
Hunspell 检索和建议有相邻键字符键构成的单词。而不是在字符串中相邻的字构成的字符串。建议 KEY
参数基于 QWERTY 和 Dvorak 键盘布局：

    KEY qwertyuiop|asdfghjkl|zxcvbnm
    KEY pyfgcrl|aeouidhtns|qjkxbmwvz

使用第一个 QWERTY 布局，Hunspell 对于 "*nide" 会建议 "nude" 和 "node"。一个字符会有更多的邻键：

    KEY qwertzuop|yxcvbnm|qaw|say|wse|dsx|sy|edr|fdc|dx|rft|gfv|fc|tgz|hgb|gv|zhu|jhn|hb|uji|kjm|jn|iko|lkm

#### TRY characters
      当 Hunspell 通过 TRY 字符发现一个可能的错误输入单词时会建议正确的词形。TRY 参数大小写敏感。

#### NOSUGGEST flag
带有 NOSUGGEST 标记的单词不会推荐修改词（但仍会接受正确输入）。建议为粗俗和淫秽词语添加标记
（参见 SUBSTANDARD）。

#### MAXCPDSUGS num
设置由组合规则生成的推荐词的最大值。推荐词的最大值可能大于同样的 1 字符距离类型。

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
如果输入词以点结束，启用点启动推荐。（不适用于 LibreOffice 词典，因为 LibreOffice
有自动启用点推荐机制。）

    REP 替换定义行数
    REP 替换什么

此表规定首先尝试修改的词汇。第一行 REP 是此表表头表明接下来的多少行是 REP 数据。
通过此表，Hunspell 为典型超过一个字母的错误拼写推荐正确的形式（查阅“ph:”）.
搜索字符串支持正则表达式的边界符（^ 和 $）。例如一个可能的英文处理错误拼写辅音
替换表定义如下：

    REP 5
    REP f ph
    REP ph f
    REP tion$ shun
    REP ^cooccurr co-occurr
    REP ^alot$ a_lot

注 I：这对大多数一个字符的错误拼写很有用，另：用 REP 你可以添加更高级的 TRY 建议子集（
建议列表以 REP 建议来时）。

注 II：建议分割单词以下划线代替空格：

    REP 1
    REP onetwothree one_two_three

注  III：替换表可通过 CHECKCOMPOUNDREP 参数用于更严格的词组检查。

    MAP 映射定义行数
    MAP 相关字符或者括号内的字符序列组成的字符串

We can define language-dependent information on characters and  character  sequences
that  should  be considered related (i.e. nearer than other chars not in the set) in
the affix file (.aff)  by a map table.  With this table, Hunspell  can  suggest  the
right  forms  for  words, which incorrectly choose the wrong letter or letter groups
from a related set more than once in a word (see REP).

For example a possible mapping could be for the German umlauted ü versus the regular
u;  the  word  Frühstück  really should be written with umlauted u's and not regular
ones

    MAP 1
    MAP uü

Use parenthesized groups for character sequences (eg. for composed Unicode characters):

    MAP 3
    MAP ß(ss)  (character sequence)
    MAP ﬁ(fi)  ("fi" compatibility characters for Unicode fi ligature)
    MAP (ó)o   (composed Unicode character: ó with bottom dot)

    PHONE number_of_phone_definitions
    PHONE 替换内容

    PHONE 借鉴了来自于 Aspell 的表驱动的音标转换算法。它对于不以发音为基础的语言系统
    是很有用的。你可以添加一个全部的字母转换和其他转化到特定字母序列的规则。更多细节
    参阅文档 http://aspell.net/man-html/Phonetic-Code.html。注：多字节 UTF-8 字符还不能支
    持括号表达式。破折号表达式可用于字节，但是不支持 UTF-8 字符。

#### WARN 标记
此标记很少词能用到，这些词通常是经常拼写错误的，查阅 Hunspell 命令行的 -r 参数和
FORBIDWARN 选项。

#### FORBIDWARN
使用 WARN 标记不能被拼写检查接受的词使用此参数。

### 组合选项
       BREAK 定义截断行数

       BREAK 符号或者符号序列
              Define new break points for breaking words and checking word parts separately. Use ^
              and  $ to delete characters at end and start of the word. Rationale: useful for com‐
              pounding with joining character or strings (for example, hyphen in English and  Ger‐
              man  or  hyphen and n-dash in Hungarian). Dashes are often bad break points for tok‐
              enization, because compounds with dashes may contain not valid  parts,  too.)   With
              BREAK, Hunspell can check both side of these compounds, breaking the words at dashes
              and n-dashes:

              BREAK 2
              BREAK -
              BREAK --    # n-dash

       Breaking are recursive, so foo-bar, bar-foo and foo-foo--bar-bar would be valid  compounds.
       Note: The default word break of Hunspell is equivalent of the following BREAK definition:

              BREAK 3
              BREAK -
              BREAK ^-
              BREAK -$

       Hunspell doesn't accept the "-word" and "word-" forms by this BREAK definition:

              BREAK 1
              BREAK -

       Switching off the default values:

              BREAK 0

       Note  II: COMPOUNDRULE is better for handling dashes and other  compound joining characters
       or character strings. Use BREAK, if you want to check words with dashes  or  other  joining
       characters and there is no time or possibility to describe precise compound rules with COM‐
       POUNDRULE (COMPOUNDRULE handles only the suffixation of the last word part  of  a  compound
       word).

       Note III: For command line spell checking of words with extra characters, set WORDCHARS pa‐
       rameters: WORDCHARS --- (see tests/break.*) example

       COMPOUNDRULE number_of_compound_definitions

       COMPOUNDRULE compound_pattern
              Define custom compound patterns with a regex-like syntax.  The first COMPOUNDRULE is
              a  header  with  the number of the following COMPOUNDRULE definitions. Compound pat‐
              terns consist compound flags, parentheses, star and question mark meta characters. A
              flag  followed by a `*' matches a word sequence of 0 or more matches of words signed
              with this compound flag.  A flag followed by a `?' matches a word sequence of 0 or 1
              matches of a word signed with this compound flag.  See tests/compound*.* examples.

              Note: en_US dictionary of OpenOffice.org uses COMPOUNDRULE for ordinal number recog‐
              nition (1st, 2nd, 11th, 12th, 22nd, 112th, 1000122nd etc.).

              Note II: In the case of long and numerical flag types use only parenthesized  flags:
              (1500)*(2000)?

              Note  III: COMPOUNDRULE flags work completely separately from the compounding mecha‐
              nisms using COMPOUNDFLAG, COMPOUNDBEGIN, etc. compound flags. (Use  these  flags  on
              different entries for words).

       COMPOUNDMIN num
              Minimum length of words used for compounding.  Default value is 3 letters.

       COMPOUNDFLAG flag
              Words  signed  with  COMPOUNDFLAG may be in compound words (except when word shorter
              than COMPOUNDMIN). Affixes with COMPOUNDFLAG also  permits  compounding  of  affixed
              words.

       COMPOUNDBEGIN flag
              Words  signed  with  COMPOUNDBEGIN (or with a signed affix) may be first elements in
              compound words.

       COMPOUNDLAST flag
              Words signed with COMPOUNDLAST (or with a signed affix) may be last elements in com‐
              pound words.

       COMPOUNDMIDDLE flag
              Words  signed with COMPOUNDMIDDLE (or with a signed affix) may be middle elements in
              compound words.

       ONLYINCOMPOUND flag
              Suffixes signed with ONLYINCOMPOUND flag may be only inside of compounds  (Fuge-ele‐
              ments  in  German,  fogemorphemes  in Swedish).  ONLYINCOMPOUND flag works also with
              words (see tests/onlyincompound.*).  Note: also valuable to flag  compounding  parts
              which are not correct as a word by itself.

       COMPOUNDPERMITFLAG flag
              Prefixes  are allowed at the beginning of compounds, suffixes are allowed at the end
              of compounds by default.  Affixes with COMPOUNDPERMITFLAG  may  be  inside  of  com‐
              pounds.

       COMPOUNDFORBIDFLAG flag
              Suffixes  with  this  flag forbid compounding of the affixed word.  Dictionary words
              with this flag are removed from the beginning and middle of compound words, overrid‐
              ing the effect of COMPOUNDPERMITFLAG.

       COMPOUNDMORESUFFIXES
              Allow twofold suffixes within compounds.

       COMPOUNDROOT flag
              COMPOUNDROOT  flag signs the compounds in the dictionary (Now it is used only in the
              Hungarian language specific code).

       COMPOUNDWORDMAX number
              Set maximum word count in a compound word. (Default is unlimited.)

       CHECKCOMPOUNDDUP
              Forbid word duplication in compounds (e.g. foofoo).

       CHECKCOMPOUNDREP
              Forbid compounding, if the (usually bad) compound word may be  a  non-compound  word
              with a REP fault. Useful for languages with `compound friendly' orthography.

       CHECKCOMPOUNDCASE
              Forbid upper case characters at word boundaries in compounds.

       CHECKCOMPOUNDTRIPLE
              Forbid  compounding, if compound word contains triple repeating letters (e.g. foo|ox
              or xo|oof). Bug: missing multi-byte character support in UTF-8 encoding (works  only
              for 7-bit ASCII characters).

       SIMPLIFIEDTRIPLE
              Allow  simplified  2-letter forms of the compounds forbidden by CHECKCOMPOUNDTRIPLE.
              It's useful  for  Swedish  and  Norwegian  (and  for  the  old  German  orthography:
              Schiff|fahrt -> Schiffahrt).

       CHECKCOMPOUNDPATTERN number_of_checkcompoundpattern_definitions

       CHECKCOMPOUNDPATTERN endchars[/flag] beginchars[/flag] [replacement]
              Forbid  compounding,  if the first word in the compound ends with endchars, and next
              word begins with beginchars and (optionally) they have the requested flags.  The op‐
              tional replacement parameter allows simplified compound form.

              The  special  "endchars"  pattern  0  (zero) limits the rule to the unmodified stems
              (stems and stems with zero affixes):

              CHECKCOMPOUNDPATTERN 0/x /y

       Note: COMPOUNDMIN doesn't work correctly with the compound word alternation, so it may need
       to set COMPOUNDMIN to lower value.

       FORCEUCASE flag
              Last word part of a compound with flag FORCEUCASE forces capitalization of the whole
              compound word. Eg. Dutch word "straat" (street) with FORCEUCASE flags  will  allowed
              only in capitalized compound forms, according to the Dutch spelling rules for proper
              names.

       COMPOUNDSYLLABLE max_syllable vowels
              Need for special compounding rules in Hungarian.  First  parameter  is  the  maximum
              syllable number, that may be in a compound, if words in compounds are more than COM‐
              POUNDWORDMAX.  Second parameter is the list of vowels (for calculating syllables).

       SYLLABLENUM flags
              Need for special compounding rules in Hungarian.

AFFIX FILE OPTIONS FOR AFFIX CREATION
       PFX flag cross_product number

       PFX flag stripping prefix [condition [morphological_fields...]]

       SFX flag cross_product number

       SFX flag stripping suffix [condition [morphological_fields...]]
              An affix is either a prefix or a suffix attached to root words to make other  words.
              We  can  define  affix classes with arbitrary number affix rules.  Affix classes are
              signed with affix flags. The first line of an affix class definition is the  header.
              The fields of an affix class header:

              (0) Option name (PFX or SFX)

              (1) Flag (name of the affix class)

              (2) Cross product (permission to combine prefixes and suffixes).  Possible values: Y
              (yes) or N (no)

              (3) Line count of the following rules.

              Fields of an affix rules:

              (0) Option name

              (1) Flag

              (2) stripping characters from beginning (at prefix rules) or end (at  suffix  rules)
              of the word

              (3) affix (optionally with flags of continuation classes, separated by a slash)

              (4) condition.

              Zero  stripping  or affix are indicated by zero. Zero condition is indicated by dot.
              Condition is a simplified, regular expression-like pattern, which must be met before
              the  affix  can  be applied. (Dot signs an arbitrary character. Characters in braces
              sign an arbitrary character from the character subset. Dash hasn't got special mean‐
              ing, but circumflex (^) next the first brace sets the complementer character set.)

              (5) Optional morphological fields separated by spaces or tabulators.

AFFIX FILE OTHER OPTIONS
       CIRCUMFIX flag
              Affixes signed with CIRCUMFIX flag may be on a word when this word also has a prefix
              with CIRCUMFIX flag and vice versa (see circumfix.* test files in the source distri‐
              bution).

       FORBIDDENWORD flag
              This  flag  signs  forbidden word form. Because affixed forms are also forbidden, we
              can subtract a subset from set of the accepted affixed and  compound  words.   Note:
              usefull to forbid erroneous words, generated by the compounding mechanism.

       FULLSTRIP
              With  FULLSTRIP, affix rules can strip full words, not only one less characters, be‐
              fore adding the affixes, see fullstrip.* test files  in  the  source  distribution).
              Note: conditions may be word length without FULLSTRIP, too.

       KEEPCASE flag
              Forbid  uppercased and capitalized forms of words signed with KEEPCASE flags. Useful
              for special orthographies (measurements and currency often keep their case in upper‐
              cased  texts) and writing systems (e.g. keeping lower case of IPA characters).  Also
              valuable for words erroneously written in the wrong case.

              Note: With CHECKSHARPS declaration, words with sharp s and KEEPCASE flag may be cap‐
              italized  and  uppercased, but uppercased forms of these words may not contain sharp
              s, only SS. See germancompounding example in the tests  directory  of  the  Hunspell
              distribution.

       ICONV number_of_ICONV_definitions

       ICONV pattern pattern2
              Define input conversion table.  Note: useful to convert one type of quote to another
              one, or change ligature.

       OCONV number_of_OCONV_definitions

       OCONV pattern pattern2
              Define output conversion table.

       LEMMA_PRESENT flag
              Deprecated. Use "st:" field instead of LEMMA_PRESENT.

       NEEDAFFIX flag
              This flag signs virtual stems in the dictionary, words only valid when affixed.  Ex‐
              cept,  if  the  dictionary word has a homonym or a zero affix.  NEEDAFFIX works also
              with prefixes and prefix + suffix combinations (see tests/needaffix5.*).

       PSEUDOROOT flag
              Deprecated. (Former name of the NEEDAFFIX option.)

       SUBSTANDARD flag
              SUBSTANDARD flag signs affix rules and dictionary words  (allomorphs)  not  used  in
              morphological generation and root words removed from suggestion. See also NOSUGGEST.

       WORDCHARS characters
              WORDCHARS  extends tokenizer of Hunspell command line interface with additional word
              character. For example, dot, dash, n-dash, numbers, percent sign are word  character
              in Hungarian.

       CHECKSHARPS
              SS letter pair in uppercased (German) words may be upper case sharp s (ß).  Hunspell
              can handle this special casing with the CHECKSHARPS declaration (see  also  KEEPCASE
              flag and tests/germancompounding example) in both spelling and suggestion.

Morphological analysis
       Hunspell's  dictionary items and affix rules may have optional space or tabulator separated
       morphological description fields, started with 3-character (two letters and a colon)  field
       IDs:

               word/flags po:noun is:nom

       Example:  We  define a simple resource with morphological informations, a derivative suffix
       (ds:) and a part of speech category (po:):

       Affix file:

               SFX X Y 1
               SFX X 0 able . ds:able

       Dictionary file:

               drink/X po:verb

       Test file:

               drink
               drinkable

       Test:

               $ analyze test.aff test.dic test.txt
               > drink
               analyze(drink) = po:verb
               stem(drink) = po:verb
               > drinkable
               analyze(drinkable) = po:verb ds:able
               stem(drinkable) = drinkable

       You can see in the example, that the analyzer concatenates the morphological fields in item
       and arrangement style.

Optional data fields
       Default morphological and other IDs (used in suggestion, stemming and morphological genera‐
       tion):

       ph:    Alternative transliteration for better suggestions, ie.  misspellings related to the
              special  orthography  and  pronunciation  of the word. The best way to handle common
              misspellings, so it's worth to add ph: field to the most affected few thousand  dic‐
              tionary  words  (or  word  pairs  etc.)  to  get  correct suggestions for their mis‐
              spellings.

              For example:

              Wednesday ph:wendsay ph:wensday
              Marseille ph:maarsayl

       Hunspell adds all ph: transliterations to the inner REP table, so it  will  always  suggest
       the correct word for the specified misspellings with the highest priority.

       The previous example is equivalent of the following REP definition:

              REP 6
              REP wendsay Wednesday
              REP Wendsay Wednesday
              REP wensday Wednesday
              REP Wensday Wednesday
              REP maarsayl Marseille
              REP Maarsayl Marseille

       The  asterisk  at the end of the ph: pattern means stripping the terminating character both
       from the pattern and the word in the associated REP rule:

              pretty ph:prity*

       will result

              REP 1
              REP prit prett

       REP rule, resulting the following correct suggestions

              *prity -> pretty
              *pritier -> prettier
              *pritiest -> prettiest

       Moreover, ph: fields can handle suggestions with more than two words, also  different  sug‐
       gestions for the same misspelling:

              do not know ph:dunno
              don't know ph:dunno

       results

              *dunno -> do not know, don't know

       Note: if available, ph: is used in n-gram similarity, too.

       The  ASCII  arrow  "->" in a ph: pattern means a REP rule (see REP), creating arbitrary re‐
       placement rule associated to the dictionary item:

              happy/B ph:hepy ph:hepi->happi

       results

              *hepy -> happy
              *hepiest -> happiest

       st:    Stem. Optional: default stem is the dictionary item in morphological analysis.  Stem
              field is useful for virtual stems (dictionary words with NEEDAFFIX flag) and morpho‐
              logical exceptions instead of new, single used morphological rules.

              feet  st:foot  is:plural
              mice  st:mouse is:plural
              teeth st:tooth is:plural

       Word forms with multiple stems need multiple dictionary items:

              lay po:verb st:lie is:past_2
              lay po:verb is:present
              lay po:noun

       al:    Allomorph(s). A dictionary item is the stem of its allomorphs.  Morphological gener‐
              ation needs stem, allomorph and affix fields.

              sing al:sang al:sung
              sang st:sing
              sung st:sing

       po:    Part of speech category.

       ds:    Derivational suffix(es).  Stemming doesn't remove derivational suffixes.  Morpholog‐
              ical generation depends on the order of the suffix fields.

              In affix rules:

              SFX Y Y 1
              SFX Y 0 ly . ds:ly_adj

       In the dictionary:

              ably st:able ds:ly_adj
              able al:ably

       is:    Inflectional suffix(es).  All inflectional suffixes are removed by  stemming.   Mor‐
              phological generation depends on the order of the suffix fields.

              feet st:foot is:plural

       ts:    Terminal  suffix(es).   Terminal  suffix  fields are inflectional suffix fields "re‐
              moved" by additional (not terminal) suffixes.

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

Twofold suffix stripping
       Ispell's  original algorithm strips only one suffix. Hunspell can strip another one yet (or
       a plus prefix in COMPLEXPREFIXES mode).

       The twofold suffix stripping is a significant improvement in handling of immense number  of
       suffixes, that characterize agglutinative languages.

       A  second `s' suffix (affix class Y) will be the continuation class of the suffix `able' in
       the following example:

               SFX Y Y 1
               SFX Y 0 s .

               SFX X Y 1
               SFX X 0 able/Y .

       Dictionary file:

               drink/X

       Test file:

               drink
               drinkable
               drinkables

       Test:

               $ hunspell -m -d test <test.txt
               drink st:drink
               drinkable st:drink fl:X
               drinkables st:drink fl:X fl:Y

       Theoretically with the twofold suffix stripping needs only the square root of the number of
       suffix rules, compared with a Hunspell implementation. In our practice, we could have elab‐
       orated the Hungarian inflectional morphology with twofold suffix stripping.

Extended affix classes
       Hunspell can handle more than 65000 affix classes.  There are three new syntax  for  giving
       flags in affix and dictionary files.

       FLAG long command sets 2-character flags:

                FLAG long
                SFX Y1 Y 1
                SFX Y1 0 s 1

       Dictionary record with the Y1, Z3, F? flags:

                foo/Y1Z3F?

       FLAG num command sets numerical flags separated by comma:

                FLAG num
                SFX 65000 Y 1
                SFX 65000 0 s 1

       Dictionary example:

                foo/65000,12,2756

       The third one is the Unicode character flags.

Homonyms
       Hunspell's dictionary can contain repeating elements that are homonyms:

               work/A    po:verb
               work/B    po:noun

       An affix file:

               SFX A Y 1
               SFX A 0 s . sf:sg3

               SFX B Y 1
               SFX B 0 s . is:plur

       Test file:

               works

       Test:

               $ hunspell -d test -m <testwords
               work st:work po:verb is:sg3
               work st:work po:noun is:plur

       This feature also gives a way to forbid illegal prefix/suffix combinations.

Prefix--suffix dependencies
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

Circumfix
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

Compounds
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
              Arbeitscomputern
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
              Arbeitsnehmer

       This solution is still not ideal, however, and will be replaced  by  a  pattern-based  com‐
       pound-checking  algorithm  which is closely integrated with input buffer tokenization. Pat‐
       terns describing compounds come as a separate input resource that can refer  to  high-level
       properties of constituent parts (e.g. the number of syllables, affix flags, and containment
       of hyphens). The patterns are matched against potential segmentations of compounds  to  as‐
       sess wellformedness.

Unicode character encoding
       Both  Ispell  and  Myspell  use 8-bit ASCII character encoding, which is a major deficiency
       when it comes to scalability.  Although a language like  Hungarian  has  a  standard  ASCII
       character  set  (ISO  8859-2),  it fails to allow a full implementation of Hungarian ortho‐
       graphic conventions.  For instance, the '--' symbol (n-dash) is missing from this character
       set  contrary  to  the  fact that it is not only the official symbol to delimit parenthetic
       clauses in the language, but it can be in compound words as a special 'big' hyphen.

       MySpell has got some 8-bit encoding tables, but there are languages without standard  8-bit
       encoding,  too.  For  example,  a lot of African languages have non-latin or extended latin
       characters.

       Similarly, using the original spelling of certain foreign names like Ångström or Molière is
       encouraged  by  the Hungarian spelling norm, and, since characters 'Å' and 'è' are not part
       of ISO 8859-2, when they combine with inflections containing characters only in  ISO 8859-2
       (like  elative  -ből,  allative  -től  or delative -ről with double acute), these result in
       words (like Ångströmről or Molière-től.) that can not be encoded using any single ASCII en‐
       coding scheme.

       The problems raised in relation to 8-bit ASCII encoding have long been recognized by propo‐
       nents of Unicode. It is clear that trading efficiency for encoding-independence has its ad‐
       vantages when it comes a truly multi-lingual application. There is implemented a memory and
       time efficient Unicode handling in Hunspell.  In  non-UTF-8  character  encodings  Hunspell
       works  with  the original 8-bit strings. In UTF-8 encoding, affixes and words are stored in
       UTF-8, during the analysis are handled in mostly UTF-8, under condition checking  and  sug‐
       gestion  are  converted  to UTF-16. Unicode text analysis and spell checking have a minimal
       (0-20%) time overhead and minimal or reasonable memory overhead depends from  the  language
       (its UTF-8 encoding and affixation).

Conversion of aspell dictionaries
       Aspell dictionaries can be easily converted into hunspell. Conversion steps:

       dictionary (xx.cwl -> xx.wl):

       preunzip xx.cwl
       wc -l < xx.wl > xx.dic
       cat xx.wl >> xx.dic

       affix file

       If the affix file exists, copy it:
       cp xx_affix.dat xx.aff
       If not, create it with the suitable character encoding (see xx.dat)
       echo "SET ISO8859-x" > xx.aff
       or
       echo "SET UTF-8" > xx.aff

       It's  useful to add a TRY option with the characters of the dictionary with frequency order
       to set edit distance suggestions:
       echo "TRY qwertzuiopasdfghjklyxcvbnmQWERTZUIOPASDFGHJKLYXCVBNM" >>xx.aff

Conversion of aspell dictionaries
       Aspell dictionaries can be easily converted into hunspell. Conversion steps:

       dictionary (xx.cwl -> xx.wl):

       preunzip xx.cwl
       wc -l < xx.wl > xx.dic
       cat xx.wl >> xx.dic

       affix file

       If the affix file exists, copy it:
       cp xx_affix.dat xx.aff
       If not, create it with the suitable character encoding (see xx.dat)
       echo "SET ISO8859-x" > xx.aff
       or
       echo "SET UTF-8" > xx.aff

       It's useful to add a TRY option with the characters of the dictionary with frequency  order
       to set edit distance suggestions:
       echo "TRY qwertzuiopasdfghjklyxcvbnmQWERTZUIOPASDFGHJKLYXCVBNM" >>xx.aff

SEE ALSO
       hunspell (1), ispell (1), ispell (4)

                                            2017-09-20                                 hunspell(5)
