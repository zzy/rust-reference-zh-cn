# 标记法

> [notation.md](https://github.com/rust-lang/reference/blob/master/src/notation.md)
> <br />
> commit b0e0ad6490d6517c19546b1023948986578fc378

## 语法

下表符号被用于 *Lexer* 和 *Syntax* 的语法片段：

| 符号          | 示例                      | 释义                                   |
|-------------------|-------------------------------|-------------------------------------------|
| CAPITAL           | KW_IF, INTEGER_LITERAL        | A token produced by the lexer             |
| _ItalicCamelCase_ | _LetStatement_, _Item_        | A syntactical production                  |
| `string`          | `x`, `while`, `*`             | The exact character(s)                    |
| \\x               | \\n, \\r, \\t, \\0            | The character represented by this escape  |
| x<sup>?</sup>     | `pub`<sup>?</sup>             | An optional item                          |
| x<sup>\*</sup>    | _OuterAttribute_<sup>\*</sup> | 0 or more of x                            |
| x<sup>+</sup>     |  _MacroMatch_<sup>+</sup>     | 1 or more of x                            |
| x<sup>a..b</sup>  | HEX_DIGIT<sup>1..6</sup>      | a to b repetitions of x                   |
| \|                | `u8` \| `u16`, Block \| Item  | Either one or another                     |
| [ ]               | [`b` `B`]                     | Any of the characters listed              |
| [ - ]             | [`a`-`z`]                     | Any of the characters in the range        |
| ~[ ]              | ~[`b` `B`]                    | Any characters, except those listed       |
| ~`string`         | ~`\n`, ~`*/`                  | Any characters, except this sequence      |
| ( )               | (`,` _Parameter_)<sup>?</sup> | Groups items                              |

## 字符串表组合

语法中的一些规则&mdash;尤其是[单目运算符][unary operators]，[双目运算符][binary
operators]和[关键字][keywords]&mdash;以一种简单的形式表现：可打印的字符串表。这些规则是[记号][tokens]相关规则的子集，并且被假定为词法分析阶段的结果。该阶段由一个 DFA（Deterministic Finite
Automaton，确定有限状态自动机）驱动，通过分离所有这些字符串表入口执行。

当语法中出现如 `monospace` 这样的字符串时（译者注：半角双引号 `"` 中的字符串），它是对字符串表组合中的单个成员的隐式引用。查阅[记号][tokens]以获取更多信息。

[binary operators]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[keywords]: keywords.md
[tokens]: tokens.md
[unary operators]: expressions/operator-expr.md#borrow-operators
