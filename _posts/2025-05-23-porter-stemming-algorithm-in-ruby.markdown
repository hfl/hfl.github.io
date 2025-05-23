---
layout: post
title:  "波特词干提取算法详解"
date:   2025-05-23 9:30:34 +0800
categories: porter stemming
---
以下是[波特词干提取算法](https://tartarus.org/martin/PorterStemmer/)的 Ruby 版本，来自 Ray Pereda 的 [Stemmify](https://github.com/raypereda/stemmify) GEM。接下来，我们详细解读：

~~~ruby
# Ruby 版本 Porter 词干提取法。
#
# 这是波特词干提取算法（Porter stemming algorithm），改编自
# Perl 版本代码。读懂原文的规则很容易：
#
#   Porter, 1980, An algorithm for suffix stripping, Program, Vol. 14,
#   no. 3, pp 130-137,
#
# 也可查阅 http://www.tartarus.org/~martin/PorterStemmer
#
# 欢迎发邮件到 raypereda (at) gmail.com 交流
# RDoc stuff.
# :title: Stemmify - Ruby 版 Porter Stemmer

module Stemmify
  STEP_2_LIST = {
    'ational'=>'ate', 'tional'=>'tion', 'enci'=>'ence', 'anci'=>'ance',
    'izer'=>'ize', 'bli'=>'ble',
    'alli'=>'al', 'entli'=>'ent', 'eli'=>'e', 'ousli'=>'ous',
    'ization'=>'ize', 'ation'=>'ate',
    'ator'=>'ate', 'alism'=>'al', 'iveness'=>'ive', 'fulness'=>'ful',
    'ousness'=>'ous', 'aliti'=>'al',
    'iviti'=>'ive', 'biliti'=>'ble', 'logi'=>'log'
  }

  STEP_3_LIST = {
    'icate'=>'ic', 'ative'=>'', 'alize'=>'al', 'iciti'=>'ic',
    'ical'=>'ic', 'ful'=>'', 'ness'=>''
  }

  SUFFIX_1_REGEXP = /(
                    ational  |
                    tional   |
                    enci     |
                    anci     |
                    izer     |
                    bli      |
                    alli     |
                    entli    |
                    eli      |
                    ousli    |
                    ization  |
                    ation    |
                    ator     |
                    alism    |
                    iveness  |
                    fulness  |
                    ousness  |
                    aliti    |
                    iviti    |
                    biliti   |
                    logi)$/x

  SUFFIX_2_REGEXP = /(
                      al       |
                      ance     |
                      ence     |
                      er       |
                      ic       |
                      able     |
                      ible     |
                      ant      |
                      ement    |
                      ment     |
                      ent      |
                      ou       |
                      ism      |
                      ate      |
                      iti      |
                      ous      |
                      ive      |
                      ize)$/x

  C = "[^aeiou]"         # 辅音
  V = "[aeiouy]"         # 元音
  CC = "#{C}(?>[^aeiouy]*)"  # 辅音序列
  VV = "#{V}(?>[aeiou]*)"    # 元音序列

  MGR0 = /^(#{CC})?#{VV}#{CC}/o                # [cc]vvcc... is m>0
  MEQ1 = /^(#{CC})?#{VV}#{CC}(#{VV})?$/o       # [cc]vvcc[vv] is m=1
  MGR1 = /^(#{CC})?#{VV}#{CC}#{VV}#{CC}/o      # [cc]vvccvvcc... is m>1
  VOWEL_IN_STEM   = /^(#{CC})?#{V}/o                      # vowel in stem

  def stem_porter
    # 复制给定对象并且转为字符串。
    w = self.dup.to_str

    return w if w.length < 3

    # now map initial y to Y so that the patterns never treat it as vowel
    w[0] = 'Y' if w[0] == ?y

    # Step 1a
    if w =~ /(ss|i)es$/
      w = $` + $1
    elsif w =~ /([^s])s$/
      w = $` + $1
    end

    # Step 1b
    if w =~ /eed$/
      w.chop! if $` =~ MGR0
    elsif w =~ /(ed|ing)$/
      stem = $`
      if stem =~ VOWEL_IN_STEM
        w = stem
        case w
          when /(at|bl|iz)$/             then w << "e"
          when /([^aeiouylsz])\1$/       then w.chop!
          when /^#{CC}#{V}[^aeiouwxy]$/o then w << "e"
        end
      end
    end

    if w =~ /y$/
      stem = $`
      w = stem + "i" if stem =~ VOWEL_IN_STEM
    end

    # Step 2
    if w =~ SUFFIX_1_REGEXP
      stem = $`
      suffix = $1
      if stem =~ MGR0
        w = stem + STEP_2_LIST[suffix]
      end
    end

    # Step 3
    if w =~ /(icate|ative|alize|iciti|ical|ful|ness)$/
      stem = $`
      suffix = $1
      if stem =~ MGR0
        w = stem + STEP_3_LIST[suffix]
      end
    end

    # Step 4
    if w =~ SUFFIX_2_REGEXP
      stem = $`
      if stem =~ MGR1
        w = stem
      end
    elsif w =~ /(s|t)(ion)$/
      stem = $` + $1
      if stem =~ MGR1
        w = stem
      end
    end

    #  Step 5
    if w =~ /e$/
      stem = $`
      if (stem =~ MGR1) ||
          (stem =~ MEQ1 && stem !~ /^#{CC}#{V}[^aeiouwxy]$/o)
        w = stem
      end
    end

    if w =~ /ll$/ && w =~ MGR1
      w.chop!
    end

    # and turn initial Y back to y
    w[0] = 'y' if w[0] == ?Y

    w
  end

  # make the stem_porter the default stem method, just in case we
  # feel like having multiple stemmers available later.
  alias stem stem_porter
end

# add the stem method to all Strings
class String
  include Stemmify
end
~~~
