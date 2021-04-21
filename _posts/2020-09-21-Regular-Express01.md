---
layout: post        # page:单页面,不在归档索引;post:有前后页面，索引
title: Regular Express - 正则表达式     # 标题
date: 2020-09-21    # 日期
categories:         # 集合,暂未使用
author:  jinyu      # 作者
tags: [Document,笔记]              # 标签，博客分类页面显示
comments: true      # bool,是否可以评论
toc:                # bool,是否有目录
pinned:             # bool,是否置顶
desc: 正则表达式的文档+学习笔记
---

**正则表达式**(Regular Expression/regex/regexp)，又称**正规表示式**,或者简称**正则式**，是计算机科学的一个概念。正则表达式使用单个字符串来描述、匹配一系列匹配某个句法规则的字符串。

<!-- more -->

正则表达式经常被应用于大规模批量的文本查找和修改，得到如C++，Java，php，Python,Perl等许多高级编程语言的支持，其中本文中将会使用Python语言作为例子来作为的正则表达式的应用语言。

## 正则表达式功能字符

正则表达式一般**功能字符**如下，如果希望使用原本字符需要转义字符转义

| \\ | . | ? | + | * | ( | ) | [ | ] | 

## 正则表达式表 功能符速查表

匹配对象如果有下划线，则真正匹配到的只有下划线部分

| 字符 | 描述 | 例子 | 匹配对象 |
| :-: | :-: | :-: | :-: |
| 普通字符 | 匹配相同的单个字符 | bcd | a<u>bcd</u>e |
| 特殊字符 | 特殊功能，介绍如下 | \\. | . |
| \| | 或运算，选择左右其中一个 | re1\|re2 | re1 或 re2 |
| \. | 匹配除\n之外的任意字符 | \. | a |
| ^ | 匹配字符串起始 | ^str | <u>str</u>ing |
| $ | 匹配字符串末尾 | ing$ | str<u>ing</u> |
| ? | 匹配0/1次左端的正则式 | a?b | ab 或者 b |
| * | 匹配0次及以上左端正则式 | a* | aaa |
| + | 匹配1次及以上左端正则式 | b+ | bbbb |
| () | 对正则进行分组 | (ab)+ | ababab |
| [...] | 匹配其中字符集的任意一个 | [abc] | a 或 b 或 c |
| [.-。] | 匹配.到。之间任意一个字符 | [a-z] | a 或 d 等 |
| [^...] | 不匹配此字符集中任何一个字符 | [^a-z] | 1 等非字母 |
| {N} | 匹配N次左端的正则式	 | (ab){2} | abab |
| {N,M} | 匹配M-N次左端的正则式 | (ac){1,2} | ab 或 abab |

## 正则表达式 特殊字符

| 字符 | 描述 | 例子 | 匹配对象 |
| :-: | :-: | :-: | :-: |
| \d | \d匹配任何一个十进制数字，\D不匹配任何数字 | \d\d\d | 123 |
| \w | \w匹配任何一个字母字符，\W不匹配任何字母字符 | \w\w | ab |
| \s | \s匹配任何空格字符，\S不匹配任何空格字符 | I\sLike | <u>I Like</u> |
| \b | \b匹配任何单词边界,\B不匹配任何单词边界 | \bLike | I <u>Like</u> |
| \A(\Z) | \A匹配字符串的开始，\Z匹配字符串的结尾 | \Acar | Hey.<u>Car</u> stop. |

## 正则表达式样例

匹配对象如果有下划线，则真正匹配到的只有下划线部分

| 例子 | 匹配对象举例 | 描述 |
| :-: | :-: | :-: |
| Hello | Hello | 匹配特定单词 |
| ^[0-9]*$ | 123 | 单独的数字 |
| ^\d{8}$  | 12345678 | 单独的8位的数字 |
| ^(\-\|\+)?\d+(\.\d+)?$ | -12.33 | 正数、负数、和小数 |
| ^[A-Za-z0-9]+$ | Kingfish404 | 英文和数字 |
| ^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$ | jinyu929@qq.com | Email地址 |

## Python 正则表达式应用

```python
# 引入python自带的正则包
import re 
m = re.search('def', 'abcdef')
print(m.group(0))
# 输出结果: 'def'
```

```python
import re
text = "He was carefully disguised but captured quickly by police."
regExp = "\w+ly" # 正则表达式
result = re.findall(r"\w+ly", text) # 参数为(r"正则式",待匹配式子)
print(result)
# 输出结果：['carefully', 'quickly']

# 下方语句功能与上方相同
result = re.findall(regExp, text)
print(result)
result = re.compile(regExp).findall(text)
print(result)
# 输出结果均为：['carefully', 'quickly']
```

更详细的应用见REF中的Python文档

## REF

* [正则表达式 wiki](https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)
* [正则表达式 MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)
* [re --- 正则表达式操作 &#8212; Python 3.8.6 文档](https://docs.python.org/zh-cn/3/library/re.html)
* [在线正则表达式测试](https://tool.oschina.net/regex)

PS：本文其实很早就建好了文件，不过之前一直忘记写了。