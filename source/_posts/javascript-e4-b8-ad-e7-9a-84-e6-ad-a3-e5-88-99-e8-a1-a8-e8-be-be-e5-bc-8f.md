---
title: 'Javascript中的正则表达式'
date: 2018-05-23 17:16:06
tags: [javascript,regexp]
published: true
hideInList: false
feature: 
isTop: false
---

1. RegExp类型的创建 var expression = /pattern/flags; 或者 var expression = new RegExp("pattern", "flags"); 

*   g ：全局匹配
*   i ：忽略大小写
*   m ：多行模式

2. RegExp实例的属性

*   global: true or false, 表示标志位'g'是否被设置
*   ignoreCase: true or false
*   multiline: true or false
*   lastIndex: 下一次搜索开始的字符位置
*   source: pattern的字符串表示

3. RegExp实例的方法

*   exec("target_string")
    *   ret.index: 匹配到的字符串的起始位置
    *   ret.input: "target_string"
    *   ret[0]: 匹配到的字符串
    *   ret[1 - n]: 匹配组1-n匹配到的字符串
*   test("target_string")
    *   true: 匹配到字符串
    *   false: 没有匹配到字符串
*   exec和test只有在设置了'g'标志位的情况下才回去更新exp.lastIndex

4. RegExp 构造函数属性

*   input ($_): 最近一次匹配成功的源字符串
*   lastMatch($&): 最近一次的匹配项
*   lastParen($+): 最近一次匹配的捕获组
*   leftContext($`): input字符串中匹配项之前的字符串
*   rightContext($*): input字符串中匹配项之后的字符串
*   $1-9: 第1-9个匹配组

附录： 正则表达式元字符表

Metacharacter

Description

`^`

Matches the starting position within the string. In line-based tools, it matches the starting position of any line.

`.`

Matches any single character (many applications exclude [newlines](https://en.wikipedia.org/wiki/Newline "Newline"), and exactly which characters are considered newlines is flavor-, character-encoding-, and platform-specific, but it is safe to assume that the line feed character is included). Within POSIX bracket expressions, the dot character matches a literal dot. For example, `a.c` matches "abc", etc., but `[a.c]` matches only "a", ".", or "c".

`[ ]`

A bracket expression. Matches a single character that is contained within the brackets. For example, `[abc]` matches "a", "b", or "c". `[a-z]` specifies a range which matches any lowercase letter from "a" to "z". These forms can be mixed: `[abcx-z]` matches "a", "b", "c", "x", "y", or "z", as does `[a-cx-z]`.The `-` character is treated as a literal character if it is the last or the first (after the `^`, if present) character within the brackets: `[abc-]`, `[-abc]`. Note that backslash escapes are not allowed. The `]` character can be included in a bracket expression if it is the first (after the `^`) character: `[]abc]`.

`[^ ]`

Matches a single character that is not contained within the brackets. For example, `[^abc]` matches any character other than "a", "b", or "c". `[^a-z]` matches any single character that is not a lowercase letter from "a" to "z". Likewise, literal characters and ranges can be mixed.

`$`

Matches the ending position of the string or the position just before a string-ending newline. In line-based tools, it matches the ending position of any line.

`( )`

Defines a marked subexpression. The string matched within the parentheses can be recalled later (see the next entry, `_n_`). A marked subexpression is also called a block or capturing group. **BRE mode requires `\( \)`**.

`_n_`

Matches what the _n_th marked subexpression matched, where _n_ is a digit from 1 to 9. This construct is vaguely defined in the POSIX.2 standard. Some tools allow referencing more than nine capturing groups.

`*`

Matches the preceding element zero or more times. For example, `ab*c` matches "ac", "abc", "abbbc", etc. `[xyz]*` matches "", "x", "y", "z", "zx", "zyx", "xyzzy", and so on. `(ab)*` matches "", "ab", "abab", "ababab", and so on.

`{_m_,_n_}`

Matches the preceding element at least _m_ and not more than _n_ times. For example, `a{3,5}` matches only "aaa", "aaaa", and "aaaaa". This is not found in a few older instances of regexes. **BRE mode requires `{_m_,_n_}`**.

 

Metacharacter

Description

`?`

Matches the preceding element zero or one time. For example, `ab?c` matches only "ac" or "abc".

`+`

Matches the preceding element one or more times. For example, `ab+c` matches "abc", "abbc", "abbbc", and so on, but not "ac".

`|`

The choice (also known as alternation or set union) operator matches either the expression before or the expression after the operator. For example, `abc|def` matches "abc" or "def".

 

POSIX

Non-standard

Perl/Tcl

Vim

Java

ASCII

Description

`[:ascii:]`[[30]](https://en.wikipedia.org/wiki/Regular_expression#cite_note-char-classes-emacs-list-2016-30)

`\p{ASCII}`

`[\x00-\x7F]`

ASCII characters

`[:alnum:]`

`\p{Alnum}`

`[A-Za-z0-9]`

Alphanumeric characters

`[:word:]`[_[citation needed](https://en.wikipedia.org/wiki/Wikipedia:Citation_needed "Wikipedia:Citation needed")_]

`\w`

`\w`

`\w`

`[A-Za-z0-9_]`

Alphanumeric characters plus "_"

`\W`

`\W`

`\W`

`[^A-Za-z0-9_]`

Non-word characters

`[:alpha:]`

`\a`

`\p{Alpha}`

`[A-Za-z]`

Alphabetic characters

`[:blank:]`

`\s`

`\p{Blank}`

`[ [[\t]]]`

Space and tab

`\b`

`\< \>`

`\b`

`(?<=\W)(?=\w)|(?<=\w)(?=\W)`

Word boundaries

`\B`

`(?<=\W)(?=\W)|(?<=\w)(?=\w)`

Non-word boundaries

`[:cntrl:]`

`\p{Cntrl}`

`[\x00-\x1F\x7F]`

[Control characters](https://en.wikipedia.org/wiki/Control_character "Control character")

`[:digit:]`

`\d`

`\d`

`\p{Digit}` or `\d`

`[0-9]`

Digits

`\D`

`\D`

`\D`

`[^0-9]`

Non-digits

`[:graph:]`

`\p{Graph}`

`[\x21-\x7E]`

Visible characters

`[:lower:]`

`\l`

`\p{Lower}`

`[a-z]`

Lowercase letters

`[:print:]`

`\p`

`\p{Print}`

`[\x20-\x7E]`

Visible characters and the space character

`[:punct:]`

`\p{Punct}`

``[][!"#$%&'()*+,./:;<=>?@\^_`{|}~-]``

Punctuation characters

`[:space:]`

`\s`

`\_s`

`\p{Space}` or `\s`

`[ [\t](https://en.wikipedia.org/wiki/%5Ct "\t")[\r](https://en.wikipedia.org/wiki/%5Cr "\r")[\n](https://en.wikipedia.org/wiki/%5Cn "\n")[\v](https://en.wikipedia.org/wiki/%5Cv "\v")[\f](https://en.wikipedia.org/wiki/%5Cf "\f")]`

[Whitespace characters](https://en.wikipedia.org/wiki/Whitespace_character "Whitespace character")

`\S`

`\S`

`\S`

`[^ \t\r\n\v\f]`

Non-whitespace characters

`[:upper:]`

`\u`

`\p{Upper}`

`[A-Z]`

Uppercase letters

`[:xdigit:]`

`\x`

`\p{XDigit}`

`[A-Fa-f0-9]`

Hexadecimal digits

 

常用分组语法

分类

代码/语法

说明

捕获

(exp)

匹配exp,并捕获文本到自动命名的组里

(?<name>exp)

匹配exp,并捕获文本到名称为name的组里，也可以写成(?'name'exp)

(?:exp)

匹配exp,不捕获匹配的文本，也不给此分组分配组号

零宽断言

(?=exp)

匹配exp前面的位置

(?<=exp)

匹配exp后面的位置

(?!exp)

匹配后面跟的不是exp的位置

(?<!exp)

匹配前面不是exp的位置

注释

(?#comment)

这种类型的分组不对正则表达式的处理产生任何影响，用于提供注释让人阅读

 

常用的反义代码

代码/语法

说明

\W

匹配任意不是字母，数字，下划线，汉字的字符

\S

匹配任意不是空白符的字符

\D

匹配任意非数字的字符

\B

匹配不是单词开头或结束的位置

[^x]

匹配除了x以外的任意字符

[^aeiou]

匹配除了aeiou这几个字母以外的任意字符