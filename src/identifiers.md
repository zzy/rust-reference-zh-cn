# 标识符

> [identifiers.md](https://github.com/rust-lang/reference/blob/master/src/identifiers.md)
> <br />
> commit 34d27fe8bc8b89b55da690484d1e17fbd0f25055

> **<sup>Lexer:<sup>**\
> 标识符_或_关键字：\
> &nbsp;&nbsp; &nbsp;&nbsp; [`a`-`z` `A`-`Z`]&nbsp;[`a`-`z` `A`-`Z` `0`-`9` `_`]<sup>\*</sup>\
> &nbsp;&nbsp; | `_` [`a`-`z` `A`-`Z` `0`-`9` `_`]<sup>+</sup>
>
> RAW_IDENTIFIER : `r#` IDENTIFIER_OR_KEYWORD <sub>*除外：`crate`, `self`, `super`, `Self`*</sub>
>
> NON_KEYWORD_IDENTIFIER : IDENTIFIER_OR_KEYWORD <sub>*除外：[规定关键字]或[保留关键字]*</sub>
>
> IDENTIFIER :\
> NON_KEYWORD_IDENTIFIER | RAW_IDENTIFIER

标识符是如下形式的任何非空 ASCII 字符串：

要么是——

* 首字符是字母。
* 其余字符是字母、数字，或 `_`。

要么是——

* 首字符是 `_`。
* 标识符由多个字符组成，单个 `_` 不是有效标识符。
* 其余字符是字母、数字，或 `_`。

原生标识符类似于普通标识符，但前缀是 `r#`（注意 `r#` 不包括在实际标识符中）。原生标识符与普通标识符的区别是：原生标识符可以是除上述列举的 `RAW_IDENTIFIER` 外的任何规定关键字或保留关键字。

[规定关键字]: keywords.md#strict-keywords
[保留关键字]: keywords.md#reserved-keywords
