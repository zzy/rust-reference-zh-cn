# 记号

> [tokens.md](https://github.com/rust-lang/reference/blob/master/src/tokens.md)
> <br />
> commit d69a6d13c917856aa7da839647dcdbde6d9dde46

记号是语法中的主要组合，使用正则（非递归）语言定义。Rust 源输入可以被分解为下述类型的记号：

* [关键字][Keywords]
* [标识符][identifier]
* [字面量](#字面量)
* [生命周期](#lifetimes-and-loop-labels)
* [标点符号](#punctuation)
* [分隔符](#delimiters)

本书语法中，“简单”记号采用[字符串表组合][string
table production]形式，并以`等宽（monospace）`字体显示。

[string table production]: notation.md#string-table-productions

## 字面量

字面量是一个包含单独记号（而不是一串记号）的表达式，它立即且直接的表示了它所赋的值，而不是通过名字或其它赋值规则引用它。字面量是[常量表达式](const_eval.md#constant-expressions)的一种形式，所以它（主要）在编译时赋值。

### 实例

#### 字符和字符串

| | 实例 | `#` 集合 | 字符集 | 转义 |
|-|------|---------|--------|-----|
| [字符](#字符字面量) | `'H'` | 0  | 全部 Unicode | [引号](#引号转义) & [ASCII](#ascii-转义) & [Unicode](#unicode-转义) |
| [字符串](#字符串字面量) | `"hello"` | 0 | 全部 Unicode | [引号](#引号转义) & [ASCII](#ascii-转义) & [Unicode](#unicode-转义) |
| [原生字符串](#原生字符串字面量) | `r#"hello"#` | 0 ... | 全部 Unicode | `N/A` |
| [字节](#字节字面量) | `b'H'` | 0 | 全部 ASCII   | [引号](#引号转义) & [字节](#字节转义)  |
| [字节串](#字节串字面量) | `b"hello"` | 0 | 全部 ASCII   | [引号](#引号转义) & [字节](#字节转义) |
| [原生字节串](#原生字节串字面量) | `br#"hello"#` | 0 ... | 全部 ASCII   | `N/A` |

\* 字面量两侧的 `#` 数量必须相同。

#### ASCII 转义

|   | 名称 |
|---|------|
| `\x41` | 7 位字符编码（2位，最大值为 `0x7F`） |
| `\n` | 换行符 |
| `\r` | 回车符 |
| `\t` | 制表符 |
| `\\` | 反斜线 |
| `\0` | Null/空/零值（译注：Rust 中没有 Null） |

#### 字节转义

|   | 名称 |
|---|------|
| `\x7F` | 8 位字符编码（2位） |
| `\n` | 换行符 |
| `\r` | 回车符 |
| `\t` | 制表符 |
| `\\` | 反斜线 |
| `\0` | Null/空/零值（译注：Rust 中没有 Null） |

#### Unicode 转义

|   | 名称 |
|---|------|
| `\u{7FFF}` | 24 位 Unicode 字符编码（最多6个数字） |

#### 引号转义

|   | Name |
|---|------|
| `\'` | 单引号 |
| `\"` | 双引号 |

#### 数值

| [数值字面量](#数值字面量)`*` | 实例 | 幂 | 后缀 |
|---------------------------|-------|----|-----|
| 十进制整数 | `98_222` | `N/A` | 整数后缀 |
| 十六进制整数 | `0xff` | `N/A` | 整数后缀 |
| 二进制整数 | `0o77` | `N/A` | 整数后缀 |
| 二进制整数 | `0b1111_0000` | `N/A` | 整数后缀 |
| 浮点数 | `123.0E+77` | `Optional` | 浮点数后缀 |

`*` 所有数值字面量允许 `_` 作为可视分隔符：`1_234.0E+18f64`

#### 后缀

后缀是紧跟（无空格）字面量主体部分之后的非原生标识符。

具有任意后缀的任何类型的字面量（如字符串、整数等）都是合法记号，可以传递给宏而不会产生错误。宏本身将决定如何诠释此类记号，以及是否产生错误。

```rust
macro_rules! blackhole { ($tt:tt) => () }

blackhole!("string"suffix); // 没毛病 :-)
```

但是，解析为 Rust 代码的字面量记号，其后缀是受限制的。对于非数字字面量记号，将拒绝任何后缀；而对于数字字面量，仅接受具有下表中的后缀。

| 整型 | 浮点型 |
|------|-------|
| `u8`, `i8`, `u16`, `i16`, `u32`, `i32`, `u64`, `i64`, `u128`, `i128`, `usize`, `isize` | `f32`, `f64` |

### 字符和字符串字面量

#### 字符字面量

> **<sup>Lexer</sup>**\
> CHAR_LITERAL :\
> &nbsp;&nbsp; `'` ( ~[`'` `\` \\n \\r \\t] | QUOTE_ESCAPE | ASCII_ESCAPE | UNICODE_ESCAPE ) `'`
>
> QUOTE_ESCAPE :\
> &nbsp;&nbsp; `\'` | `\"`
>
> ASCII_ESCAPE :\
> &nbsp;&nbsp; &nbsp;&nbsp; `\x` OCT_DIGIT HEX_DIGIT\
> &nbsp;&nbsp; | `\n` | `\r` | `\t` | `\\` | `\0`
>
> UNICODE_ESCAPE :\
> &nbsp;&nbsp; `\u{` ( HEX_DIGIT `_`<sup>\*</sup> )<sup>1..6</sup> `}`

_字符字面量_ 是位于两个 `U+0027`（单引号）字符内的单个 Unicode 字符。当它是 `U+0027` 自身时，须前置 _转义_ 字符 `U+005C`（`\`）。

#### 字符串字面量

> **<sup>Lexer</sup>**\
> STRING_LITERAL :\
> &nbsp;&nbsp; `"` (\
> &nbsp;&nbsp; &nbsp;&nbsp; ~[`"` `\` _IsolatedCR_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | QUOTE_ESCAPE\
> &nbsp;&nbsp; &nbsp;&nbsp; | ASCII_ESCAPE\
> &nbsp;&nbsp; &nbsp;&nbsp; | UNICODE_ESCAPE\
> &nbsp;&nbsp; &nbsp;&nbsp; | STRING_CONTINUE\
> &nbsp;&nbsp; )<sup>\*</sup> `"`
>
> STRING_CONTINUE :\
> &nbsp;&nbsp; `\` _followed by_ \\n

_字符串字面量_ 是位于两个 `U+0022`（双引号）字符内的任意 Unicode 字符序列。当它是 `U+0022` 自身时，须前置 _转义_ 字符 `U+005C`（`\`）。

字符串字面量中允许分行。分行符可以换行符（`U+000A`），也可以是回车符和一对换行符（`U+000D`, `U+000A`）。此对字节序列通常转换为 `U+000A`，但有例外：当分行符前置一个未转义的 `U+005C` 字符（`\`）时，将会导致 `U+005C` 字符、换行符和下一行开头的所有空白都被忽略。是故下述示例中，`a` 和 `b` 是等同的：

```rust
let a = "foobar";
let b = "foo\
         bar";

assert_eq!(a,b);
```

#### 字符转义

不管是字符字面量，还是非原生字符串字面量，都有一些额外 _转义_。一个转义以一个 `U+005C`（`\`）开始，并后跟如下形式之一：

* _7 位代码点转义_ 以 `U+0078`（`x`）开头，后紧跟两位 _十六进制数字_，最大值为 `0x7F`。表示其值等于它所提供的十六进制 ASCII 字符，但不能确定其是 Unicode 代码点还是字节值，所以更大的值是不被允许的。
* _24 位代码点转义_ 以 `U+0075`（`u`）开头，后跟多达六位 _十六进制数字_，位于大括号 `U+007B`（`{`）和 `U+007D`（`}`）之间。表示其值等于它所提供的十六进制 Unicode 代码点。
* _空白转义_ 是字符 `U+006E`（`n`），`U+0072`（`r`），或者 `U+0074`（`t`）之一，依次表示 Unicode 值 `U+000A`（LF），`U+000D`（CR），或者 `U+0009`（HT）。
* _null/空/零值转义_ 是字符 `U+0030`（`0`），表示 Unicode 值 `U+0000`（NUL）。
* _反斜杠转义_ 是字符 `U+005C`（`\`），必须转义才能表示其自身。

#### 原生字符串字面量

> **<sup>Lexer</sup>**\
> RAW_STRING_LITERAL :\
> &nbsp;&nbsp; `r` RAW_STRING_CONTENT
>
> RAW_STRING_CONTENT :\
> &nbsp;&nbsp; &nbsp;&nbsp; `"` ( ~ _IsolatedCR_ )<sup>* (non-greedy)</sup> `"`\
> &nbsp;&nbsp; | `#` RAW_STRING_CONTENT `#`

Raw string literals do not process any escapes. They start with the character
`U+0072` (`r`), followed by zero or more of the character `U+0023` (`#`) and a
`U+0022` (double-quote) character. The _raw string body_ can contain any sequence
of Unicode characters and is terminated only by another `U+0022` (double-quote)
character, followed by the same number of `U+0023` (`#`) characters that preceded
the opening `U+0022` (double-quote) character.

All Unicode characters contained in the raw string body represent themselves,
the characters `U+0022` (double-quote) (except when followed by at least as
many `U+0023` (`#`) characters as were used to start the raw string literal) or
`U+005C` (`\`) do not have any special meaning.

Examples for string literals:

```rust
"foo"; r"foo";                     // foo
"\"foo\""; r#""foo""#;             // "foo"

"foo #\"# bar";
r##"foo #"# bar"##;                // foo #"# bar

"\x52"; "R"; r"R";                 // R
"\\x52"; r"\x52";                  // \x52
```

### Byte and byte string literals

#### Byte literals

> **<sup>Lexer</sup>**\
> BYTE_LITERAL :\
> &nbsp;&nbsp; `b'` ( ASCII_FOR_CHAR | BYTE_ESCAPE )  `'`
>
> ASCII_FOR_CHAR :\
> &nbsp;&nbsp; _any ASCII (i.e. 0x00 to 0x7F), except_ `'`, `\`, \\n, \\r or \\t
>
> BYTE_ESCAPE :\
> &nbsp;&nbsp; &nbsp;&nbsp; `\x` HEX_DIGIT HEX_DIGIT\
> &nbsp;&nbsp; | `\n` | `\r` | `\t` | `\\` | `\0`

A _byte literal_ is a single ASCII character (in the `U+0000` to `U+007F`
range) or a single _escape_ preceded by the characters `U+0062` (`b`) and
`U+0027` (single-quote), and followed by the character `U+0027`. If the character
`U+0027` is present within the literal, it must be _escaped_ by a preceding
`U+005C` (`\`) character. It is equivalent to a `u8` unsigned 8-bit integer
_number literal_.

#### Byte string literals

> **<sup>Lexer</sup>**\
> BYTE_STRING_LITERAL :\
> &nbsp;&nbsp; `b"` ( ASCII_FOR_STRING | BYTE_ESCAPE | STRING_CONTINUE )<sup>\*</sup> `"`
>
> ASCII_FOR_STRING :\
> &nbsp;&nbsp; _any ASCII (i.e 0x00 to 0x7F), except_ `"`, `\` _and IsolatedCR_

A non-raw _byte string literal_ is a sequence of ASCII characters and _escapes_,
preceded by the characters `U+0062` (`b`) and `U+0022` (double-quote), and
followed by the character `U+0022`. If the character `U+0022` is present within
the literal, it must be _escaped_ by a preceding `U+005C` (`\`) character.
Alternatively, a byte string literal can be a _raw byte string literal_, defined
below. The type of a byte string literal of length `n` is `&'static [u8; n]`.

Some additional _escapes_ are available in either byte or non-raw byte string
literals. An escape starts with a `U+005C` (`\`) and continues with one of the
following forms:

* A _byte escape_ escape starts with `U+0078` (`x`) and is
  followed by exactly two _hex digits_. It denotes the byte
  equal to the provided hex value.
* A _whitespace escape_ is one of the characters `U+006E` (`n`), `U+0072`
  (`r`), or `U+0074` (`t`), denoting the bytes values `0x0A` (ASCII LF),
  `0x0D` (ASCII CR) or `0x09` (ASCII HT) respectively.
* The _null escape_ is the character `U+0030` (`0`) and denotes the byte
  value `0x00` (ASCII NUL).
* The _backslash escape_ is the character `U+005C` (`\`) which must be
  escaped in order to denote its ASCII encoding `0x5C`.

#### Raw byte string literals

> **<sup>Lexer</sup>**\
> RAW_BYTE_STRING_LITERAL :\
> &nbsp;&nbsp; `br` RAW_BYTE_STRING_CONTENT
>
> RAW_BYTE_STRING_CONTENT :\
> &nbsp;&nbsp; &nbsp;&nbsp; `"` ASCII<sup>* (non-greedy)</sup> `"`\
> &nbsp;&nbsp; | `#` RAW_STRING_CONTENT `#`
>
> ASCII :\
> &nbsp;&nbsp; _any ASCII (i.e. 0x00 to 0x7F)_

Raw byte string literals do not process any escapes. They start with the
character `U+0062` (`b`), followed by `U+0072` (`r`), followed by zero or more
of the character `U+0023` (`#`), and a `U+0022` (double-quote) character. The
_raw string body_ can contain any sequence of ASCII characters and is terminated
only by another `U+0022` (double-quote) character, followed by the same number of
`U+0023` (`#`) characters that preceded the opening `U+0022` (double-quote)
character. A raw byte string literal can not contain any non-ASCII byte.

All characters contained in the raw string body represent their ASCII encoding,
the characters `U+0022` (double-quote) (except when followed by at least as
many `U+0023` (`#`) characters as were used to start the raw string literal) or
`U+005C` (`\`) do not have any special meaning.

Examples for byte string literals:

```rust
b"foo"; br"foo";                     // foo
b"\"foo\""; br#""foo""#;             // "foo"

b"foo #\"# bar";
br##"foo #"# bar"##;                 // foo #"# bar

b"\x52"; b"R"; br"R";                // R
b"\\x52"; br"\x52";                  // \x52
```

### 数值字面量

A _number literal_ is either an _integer literal_ or a _floating-point
literal_. The grammar for recognizing the two kinds of literals is mixed.

#### Integer literals

> **<sup>Lexer</sup>**\
> INTEGER_LITERAL :\
> &nbsp;&nbsp; ( DEC_LITERAL | BIN_LITERAL | OCT_LITERAL | HEX_LITERAL )
>              INTEGER_SUFFIX<sup>?</sup>
>
> DEC_LITERAL :\
> &nbsp;&nbsp; DEC_DIGIT (DEC_DIGIT|`_`)<sup>\*</sup>
>
> TUPLE_INDEX :\
> &nbsp;&nbsp; &nbsp;&nbsp; `0`
> &nbsp;&nbsp; | NON_ZERO_DEC_DIGIT DEC_DIGIT<sup>\*</sup>
>
> BIN_LITERAL :\
> &nbsp;&nbsp; `0b` (BIN_DIGIT|`_`)<sup>\*</sup> BIN_DIGIT (BIN_DIGIT|`_`)<sup>\*</sup>
>
> OCT_LITERAL :\
> &nbsp;&nbsp; `0o` (OCT_DIGIT|`_`)<sup>\*</sup> OCT_DIGIT (OCT_DIGIT|`_`)<sup>\*</sup>
>
> HEX_LITERAL :\
> &nbsp;&nbsp; `0x` (HEX_DIGIT|`_`)<sup>\*</sup> HEX_DIGIT (HEX_DIGIT|`_`)<sup>\*</sup>
>
> BIN_DIGIT : [`0`-`1`]
>
> OCT_DIGIT : [`0`-`7`]
>
> DEC_DIGIT : [`0`-`9`]
>
> NON_ZERO_DEC_DIGIT : [`1`-`9`]
>
> HEX_DIGIT : [`0`-`9` `a`-`f` `A`-`F`]
>
> INTEGER_SUFFIX :\
> &nbsp;&nbsp; &nbsp;&nbsp; `u8` | `u16` | `u32` | `u64` | `u128` | `usize`\
> &nbsp;&nbsp; | `i8` | `i16` | `i32` | `i64` | `i128` | `isize`

An _integer literal_ has one of four forms:

* A _decimal literal_ starts with a *decimal digit* and continues with any
  mixture of *decimal digits* and _underscores_.
* A _tuple index_ is either `0`, or starts with a *non-zero decimal digit* and
  continues with zero or more decimal digits. Tuple indexes are used to refer
  to the fields of [tuples], [tuple structs], and [tuple variants].
* A _hex literal_ starts with the character sequence `U+0030` `U+0078`
  (`0x`) and continues as any mixture (with at least one digit) of hex digits
  and underscores.
* An _octal literal_ starts with the character sequence `U+0030` `U+006F`
  (`0o`) and continues as any mixture (with at least one digit) of octal digits
  and underscores.
* A _binary literal_ starts with the character sequence `U+0030` `U+0062`
  (`0b`) and continues as any mixture (with at least one digit) of binary digits
  and underscores.

Like any literal, an integer literal may be followed (immediately,
without any spaces) by an _integer suffix_, which forcibly sets the
type of the literal. The integer suffix must be the name of one of the
integral types: `u8`, `i8`, `u16`, `i16`, `u32`, `i32`, `u64`, `i64`,
`u128`, `i128`, `usize`, or `isize`.

The type of an _unsuffixed_ integer literal is determined by type inference:

* If an integer type can be _uniquely_ determined from the surrounding
  program context, the unsuffixed integer literal has that type.

* If the program context under-constrains the type, it defaults to the
  signed 32-bit integer `i32`.

* If the program context over-constrains the type, it is considered a
  static type error.

Examples of integer literals of various forms:

```rust
123;                               // type i32
123i32;                            // type i32
123u32;                            // type u32
123_u32;                           // type u32
let a: u64 = 123;                  // type u64

0xff;                              // type i32
0xff_u8;                           // type u8

0o70;                              // type i32
0o70_i16;                          // type i16

0b1111_1111_1001_0000;             // type i32
0b1111_1111_1001_0000i64;          // type i64
0b________1;                       // type i32

0usize;                            // type usize
```

Examples of invalid integer literals:

```rust,compile_fail
// invalid suffixes

0invalidSuffix;

// uses numbers of the wrong base

123AFB43;
0b0102;
0o0581;

// integers too big for their type (they overflow)

128_i8;
256_u8;

// bin, hex, and octal literals must have at least one digit

0b_;
0b____;
```

Note that the Rust syntax considers `-1i8` as an application of the [unary minus
operator] to an integer literal `1i8`, rather than
a single integer literal.

[unary minus operator]: expressions/operator-expr.md#negation-operators

#### Floating-point literals

> **<sup>Lexer</sup>**\
> FLOAT_LITERAL :\
> &nbsp;&nbsp; &nbsp;&nbsp; DEC_LITERAL `.`
>   _(not immediately followed by `.`, `_` or an [identifier]_)\
> &nbsp;&nbsp; | DEC_LITERAL FLOAT_EXPONENT\
> &nbsp;&nbsp; | DEC_LITERAL `.` DEC_LITERAL FLOAT_EXPONENT<sup>?</sup>\
> &nbsp;&nbsp; | DEC_LITERAL (`.` DEC_LITERAL)<sup>?</sup>
>                    FLOAT_EXPONENT<sup>?</sup> FLOAT_SUFFIX
>
> FLOAT_EXPONENT :\
> &nbsp;&nbsp; (`e`|`E`) (`+`|`-`)?
>               (DEC_DIGIT|`_`)<sup>\*</sup> DEC_DIGIT (DEC_DIGIT|`_`)<sup>\*</sup>
>
> FLOAT_SUFFIX :\
> &nbsp;&nbsp; `f32` | `f64`

A _floating-point literal_ has one of two forms:

* A _decimal literal_ followed by a period character `U+002E` (`.`). This is
  optionally followed by another decimal literal, with an optional _exponent_.
* A single _decimal literal_ followed by an _exponent_.

Like integer literals, a floating-point literal may be followed by a
suffix, so long as the pre-suffix part does not end with `U+002E` (`.`).
The suffix forcibly sets the type of the literal. There are two valid
_floating-point suffixes_, `f32` and `f64` (the 32-bit and 64-bit floating point
types), which explicitly determine the type of the literal.

The type of an _unsuffixed_ floating-point literal is determined by
type inference:

* If a floating-point type can be _uniquely_ determined from the
  surrounding program context, the unsuffixed floating-point literal
  has that type.

* If the program context under-constrains the type, it defaults to `f64`.

* If the program context over-constrains the type, it is considered a
  static type error.

Examples of floating-point literals of various forms:

```rust
123.0f64;        // type f64
0.1f64;          // type f64
0.1f32;          // type f32
12E+99_f64;      // type f64
let x: f64 = 2.; // type f64
```

This last example is different because it is not possible to use the suffix
syntax with a floating point literal ending in a period. `2.f64` would attempt
to call a method named `f64` on `2`.

The representation semantics of floating-point numbers are described in
["Machine Types"].

["Machine Types"]: types/numeric.md

### Boolean literals

> **<sup>Lexer</sup>**\
> BOOLEAN_LITERAL :\
> &nbsp;&nbsp; &nbsp;&nbsp; `true`\
> &nbsp;&nbsp; | `false`

The two values of the boolean type are written `true` and `false`.

## Lifetimes and loop labels

> **<sup>Lexer</sup>**\
> LIFETIME_TOKEN :\
> &nbsp;&nbsp; &nbsp;&nbsp; `'` [IDENTIFIER_OR_KEYWORD][identifier]\
> &nbsp;&nbsp; | `'_`
>
> LIFETIME_OR_LABEL :\
> &nbsp;&nbsp; &nbsp;&nbsp; `'` [NON_KEYWORD_IDENTIFIER][identifier]

Lifetime parameters and [loop labels] use LIFETIME_OR_LABEL tokens. Any
LIFETIME_TOKEN will be accepted by the lexer, and for example, can be used in
macros.

[loop labels]: expressions/loop-expr.md

## Punctuation

Punctuation symbol tokens are listed here for completeness. Their individual
usages and meanings are defined in the linked pages.

| Symbol | Name        | Usage |
|--------|-------------|-------|
| `+`    | Plus        | [Addition][arith], [Trait Bounds], [Macro Kleene Matcher][macros]
| `-`    | Minus       | [Subtraction][arith], [Negation]
| `*`    | Star        | [Multiplication][arith], [Dereference], [Raw Pointers], [Macro Kleene Matcher][macros]
| `/`    | Slash       | [Division][arith]
| `%`    | Percent     | [Remainder][arith]
| `^`    | Caret       | [Bitwise and Logical XOR][arith]
| `!`    | Not         | [Bitwise and Logical NOT][negation], [Macro Calls][macros], [Inner Attributes][attributes], [Never Type]
| `&`    | And         | [Bitwise and Logical AND][arith], [Borrow], [References], [Reference patterns]
| <code>\|</code> | Or | [Bitwise and Logical OR][arith], [Closures], [Match]
| `&&`   | AndAnd      | [Lazy AND][lazy-bool], [Borrow], [References], [Reference patterns]
| <code>\|\|</code> | OrOr | [Lazy OR][lazy-bool], [Closures]
| `<<`   | Shl         | [Shift Left][arith], [Nested Generics][generics]
| `>>`   | Shr         | [Shift Right][arith], [Nested Generics][generics]
| `+=`   | PlusEq      | [Addition assignment][compound]
| `-=`   | MinusEq     | [Subtraction assignment][compound]
| `*=`   | StarEq      | [Multiplication assignment][compound]
| `/=`   | SlashEq     | [Division assignment][compound]
| `%=`   | PercentEq   | [Remainder assignment][compound]
| `^=`   | CaretEq     | [Bitwise XOR assignment][compound]
| `&=`   | AndEq       | [Bitwise And assignment][compound]
| <code>\|=</code> | OrEq | [Bitwise Or assignment][compound]
| `<<=`  | ShlEq       | [Shift Left assignment][compound]
| `>>=`  | ShrEq       | [Shift Right assignment][compound], [Nested Generics][generics]
| `=`    | Eq          | [Assignment], [Attributes], Various type definitions
| `==`   | EqEq        | [Equal][comparison]
| `!=`   | Ne          | [Not Equal][comparison]
| `>`    | Gt          | [Greater than][comparison], [Generics], [Paths]
| `<`    | Lt          | [Less than][comparison], [Generics], [Paths]
| `>=`   | Ge          | [Greater than or equal to][comparison], [Generics]
| `<=`   | Le          | [Less than or equal to][comparison]
| `@`    | At          | [Subpattern binding]
| `_`    | Underscore  | [Wildcard patterns], [Inferred types]
| `.`    | Dot         | [Field access][field], [Tuple index]
| `..`   | DotDot      | [Range][range], [Struct expressions], [Patterns]
| `...`  | DotDotDot   | [Variadic functions][extern], [Range patterns]
| `..=`  | DotDotEq    | [Inclusive Range][range], [Range patterns]
| `,`    | Comma       | Various separators
| `;`    | Semi        | Terminator for various items and statements, [Array types]
| `:`    | Colon       | Various separators
| `::`   | PathSep     | [Path separator][paths]
| `->`   | RArrow      | [Function return type][functions], [Closure return type][closures]
| `=>`   | FatArrow    | [Match arms][match], [Macros]
| `#`    | Pound       | [Attributes]
| `$`    | Dollar      | [Macros]
| `?`    | Question    | [Question mark operator][question], [Questionably sized][sized], [Macro Kleene Matcher][macros]

## Delimiters

Bracket punctuation is used in various parts of the grammar. An open bracket
must always be paired with a close bracket. Brackets and the tokens within
them are referred to as "token trees" in [macros].  The three types of brackets are:

| Bracket | Type            |
|---------|-----------------|
| `{` `}` | Curly braces    |
| `[` `]` | Square brackets |
| `(` `)` | Parentheses     |


[Inferred types]: types/inferred.md
[Range patterns]: patterns.md#range-patterns
[Reference patterns]: patterns.md#reference-patterns
[Subpattern binding]: patterns.md#identifier-patterns
[Wildcard patterns]: patterns.md#wildcard-pattern
[arith]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[array types]: types/array.md
[assignment]: expressions/operator-expr.md#assignment-expressions
[attributes]: attributes.md
[borrow]: expressions/operator-expr.md#borrow-operators
[closures]: expressions/closure-expr.md
[comparison]: expressions/operator-expr.md#comparison-operators
[compound]: expressions/operator-expr.md#compound-assignment-expressions
[dereference]: expressions/operator-expr.md#the-dereference-operator
[extern]: items/external-blocks.md
[field]: expressions/field-expr.md
[functions]: items/functions.md
[generics]: items/generics.md
[identifier]: identifiers.md
[keywords]: keywords.md
[lazy-bool]: expressions/operator-expr.md#lazy-boolean-operators
[macros]: macros-by-example.md
[match]: expressions/match-expr.md
[negation]: expressions/operator-expr.md#negation-operators
[never type]: types/never.md
[paths]: paths.md
[patterns]: patterns.md
[question]: expressions/operator-expr.md#the-question-mark-operator
[range]: expressions/range-expr.md
[raw pointers]: types/pointer.md#raw-pointers-const-and-mut
[references]: types/pointer.md
[sized]: trait-bounds.md#sized
[struct expressions]: expressions/struct-expr.md
[trait bounds]: trait-bounds.md
[tuple index]: expressions/tuple-expr.md#tuple-indexing-expressions
[tuple structs]: items/structs.md
[tuple variants]: items/enumerations.md
[tuples]: types/tuple.md
