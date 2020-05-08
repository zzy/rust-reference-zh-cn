# 声明宏

> [macros-by-example.md](https://github.com/rust-lang/reference/blob/master/src/macros-by-example.md)
> <br />
> commit bdf9fd191fe3c83d04e7143a9aa4075056cd945e 2019-12-22

> **<sup>Syntax</sup>**\
> _MacroRulesDefinition_ :\
> &nbsp;&nbsp; `macro_rules` `!` [IDENTIFIER] _MacroRulesDef_
>
> _MacroRulesDef_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _MacroRules_ `)` `;`\
> &nbsp;&nbsp; | `[` _MacroRules_ `]` `;`\
> &nbsp;&nbsp; | `{` _MacroRules_ `}`
>
> _MacroRules_ :\
> &nbsp;&nbsp; _MacroRule_ ( `;` _MacroRule_ )<sup>\*</sup> `;`<sup>?</sup>
>
> _MacroRule_ :\
> &nbsp;&nbsp; _MacroMatcher_ `=>` _MacroTranscriber_
>
> _MacroMatcher_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _MacroMatch_<sup>\*</sup> `)`\
> &nbsp;&nbsp; | `[` _MacroMatch_<sup>\*</sup> `]`\
> &nbsp;&nbsp; | `{` _MacroMatch_<sup>\*</sup> `}`
>
> _MacroMatch_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Token_]<sub>_except $ and delimiters_</sub>\
> &nbsp;&nbsp; | _MacroMatcher_\
> &nbsp;&nbsp; | `$` [IDENTIFIER] `:` _MacroFragSpec_\
> &nbsp;&nbsp; | `$` `(` _MacroMatch_<sup>+</sup> `)` _MacroRepSep_<sup>?</sup> _MacroRepOp_
>
> _MacroFragSpec_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `block` | `expr` | `ident` | `item` | `lifetime` | `literal`\
> &nbsp;&nbsp; | `meta` | `pat` | `path` | `stmt` | `tt` | `ty` | `vis`
>
> _MacroRepSep_ :\
> &nbsp;&nbsp; [_Token_]<sub>_except delimiters and repetition operators_</sub>
>
> _MacroRepOp_ :\
> &nbsp;&nbsp; `*` | `+` | `?`
>
> _MacroTranscriber_ :\
> &nbsp;&nbsp; [_DelimTokenTree_]

`声明宏`允许用户以声明的方式定义语法扩展，我们称这种扩展为“示例宏（macros by example）”，或简单的称作“宏（macros）”。

每个声明宏有其名字，以及一个或多个 _规则_。每个规则包含两个部分：一个是 _匹配器_——描述声明宏的匹配语法；另一个是 _转换器_——描述声明宏被成功匹配和调用后的替换语法。匹配器和转换器都必须由分隔符包围。宏可以扩展为表达式、语句、项（包括 trait、实现，以及外部项）、类型，或者模式。

## 转换

当宏被调用时，声明宏的扩展程序根据名称查找宏调用，并依次尝试每个宏规则。声明宏会根据第一个成功的匹配进行转换；即使转换结果导致错误，也不会尝试后续的匹配。声明宏执行匹配时，不执行预判；如果编译器无法确定如何解析一个标记的宏调用，则会导致错误。下述示例中，编译器不会预判传入的标识符以查看其跟随记号是否为“`)`”——尽管预判将允许编译器明确地解析调用：

```rust,compile_fail
macro_rules! ambiguity {
    ($($i:ident)* $j:ident) => { };
}

ambiguity!(error); // Error: local ambiguity
```

在匹配器和转换器中，`$` 记号用于调用宏引擎中的特殊行为（下文的[元变量][Metavariables]和[重复][Repetitions]中有详述）。不属于这种调用的记号是按字面意思匹配和转换的——只有一个例外：匹配器的外部分隔符将匹配任何一对分隔符。因此，比如匹配器 `(())` 将匹配 `{()}` 而不是 `{{}}`。字符 `$` 不能被匹配或按照字面意义转换。

当将匹配片段发送到另一个声明宏时，第二个宏中的匹配器将看到此匹配片段类型的不完全抽象语法树（AST）。第二个宏不能根据记号的字面意义来匹配匹配器中的片段，只能使用同一类型的片段分类符。匹配片段的类型 `ident`、`lifetime`、`tt` 是例外状况，_可以_ 通过记号的字面意义进行匹配。如下通过例子阐述上述限制：

```rust,compile_fail
macro_rules! foo {
    ($l:expr) => { bar!($l); }
// ERROR:               ^^ no rules expected this token in macro call
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```

下述例子说明如何在匹配 `tt` 片段后直接匹配记号：

```rust
// compiles OK
macro_rules! foo {
    ($l:tt) => { bar!($l); }
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```

## 元变量

在匹配器中，`$`_名字_ `:` _片段分类符_ 匹配指定类型的 Rust 语法片段，并将其绑定到元变量 `$`_名字_。有效的片段分类符是：

  * `item`：[项][_Item_]
  * `block`：[块表达式][_BlockExpression_]
  * `stmt`：末尾没有分号的[语句][_Statement_]（需要分号的项语句除外）
  * `pat`：[模式][_Pattern_]
  * `expr`：[表达式][_Expression_]
  * `ty`：[类型][_Type_]
  * `ident`：[标识符/关键字][IDENTIFIER_OR_KEYWORD]
  * `path`：[类型路径][_TypePath_]风格的路径
  * `tt`：[记号树][_TokenTree_]&nbsp;（匹配分隔符 `()`、`[]`、`{}` 中的单个或多个[记号][token])
  * `meta`：[属性][_Attr_]，属性的内容
  * `lifetime`：[生命周期标记][LIFETIME_TOKEN]
  * `vis`：可能为空的[可见性][_Visibility_]限定符
  * `literal`：匹配“`-`”<sup>?</sup>[字面量表达式][_LiteralExpression_]


在转换器中，因为片段类型是在匹配器中指定的，所以元变量仅被 `$`_名称_ 简单引用。元变量将被替换为与片段类型相匹配的语法元素；元变量关键字 `$crate` 可用于引用当前 crate（详细参见[辅助][Hygiene]）；元变量可以被多次转换，也可以根本不转换。 

## 重复

在匹配器和转换器中，通过将要重复的记号放在 `$(`…`)` 内来代表重复，可选后跟一个分隔记号，然后后跟重复运算符。分隔记号可以是除分隔符或某个重复运算符之外的任何记号，分号 `;` 和逗号 `,` 是最常用的。例如：`$( $i:ident ),*` 表示用逗号分隔的任何数量的标识符，且允许嵌套式重复。 

重复运算符包括：

- `*` — 代表任意数量的重复——即 `0` 次或`多`次重复。
- `+` — 代表至少 `1` 次的重复。
- `?` — 代表出现 `0` 次或者 `1` 次的可选片段。

因为 `?` 最多代表出现一个匹配项，所以不能和分隔符一起使用。

重复的片段可匹配并转换为指定数量的片段，由分隔符分隔。元变量被匹配到与其相符的每个重复匹配。例如：上述示例 `$( $i:ident
),*` 将匹配 `$i` 与列表中的所有标识符相符。

在转换过程中，会有附加的限制用于重复操作，以便于编译器知道如何正确地展开重复的记号：

1. 首先，元变量在转换器中出现的数量、种类，以及重复的嵌套顺序，与其在匹配器中出现的完全相同。所以若匹配器中出现 `$( $i:ident ),*`，那么转换器中出现的 `=> { $i }`、`=> { $( $( $i)* )* }`，以及 `=> { $( $i )+ }` 都是不合法的。而 `=> { $( $i );* }` 是正确的，并用分号分隔的列表替换逗号分隔的标识符列表。
1. 其次，转换器中的每个重复则必须包含至少一个元变量，从而决定其扩展的次数。如果在同一个重复中出现多个元变量，则它们必须被绑定到相同数量的片段上。例如：`( $( $i:ident ),* ; $( $j:ident ),* ) =>
    ( $( ($i,$j) ),*` 必须绑定到与 `$i` 片段同等数量的 `$j` 片段，这意味着使用 `(a, b, c; d, e, f)` 调用宏是合法的，并且可扩展为 `((a,d)、(b,e)、(c,f))`。但是 `(a, b, c; d, e)` 不合法的，因为其数量不同。此要求适用于嵌套重复的每一层。

## 作用域、导出，以及导入

For historical reasons, the scoping of macros by example does not work entirely like
items. Macros have two forms of scope: textual scope, and path-based scope.
Textual scope is based on the order that things appear in source files, or even
across multiple files, and is the default scoping. It is explained further below.
Path-based scope works exactly the same way that item scoping does. The scoping,
exporting, and importing of macros is controlled largely by attributes.

When a macro is invoked by an unqualified identifier (not part of a multi-part
path), it is first looked up in textual scoping. If this does not yield any
results, then it is looked up in path-based scoping. If the macro's name is
qualified with a path, then it is only looked up in path-based scoping.

<!-- ignore: requires external crates -->
```rust,ignore
use lazy_static::lazy_static; // Path-based import.

macro_rules! lazy_static { // Textual definition.
    (lazy) => {};
}

lazy_static!{lazy} // Textual lookup finds our macro first.
self::lazy_static!{} // Path-based lookup ignores our macro, finds imported one.
```

### Textual Scope

Textual scope is based largely on the order that things appear in source files,
and works similarly to the scope of local variables declared with `let` except
it also applies at the module level. When `macro_rules!` is used to define a
macro, the macro enters the scope after the definition (note that it can still
be used recursively, since names are looked up from the invocation site), up
until its surrounding scope, typically a module, is closed. This can enter child
modules and even span across multiple files:

<!-- ignore: requires external modules -->
```rust,ignore
//// src/lib.rs
mod has_macro {
    // m!{} // Error: m is not in scope.

    macro_rules! m {
        () => {};
    }
    m!{} // OK: appears after declaration of m.

    mod uses_macro;
}

// m!{} // Error: m is not in scope.

//// src/has_macro/uses_macro.rs

m!{} // OK: appears after declaration of m in src/lib.rs
```

It is not an error to define a macro multiple times; the most recent declaration
will shadow the previous one unless it has gone out of scope.

```rust
macro_rules! m {
    (1) => {};
}

m!(1);

mod inner {
    m!(1);

    macro_rules! m {
        (2) => {};
    }
    // m!(1); // Error: no rule matches '1'
    m!(2);

    macro_rules! m {
        (3) => {};
    }
    m!(3);
}

m!(1);
```

Macros can be declared and used locally inside functions as well, and work
similarly:

```rust
fn foo() {
    // m!(); // Error: m is not in scope.
    macro_rules! m {
        () => {};
    }
    m!();
}


// m!(); // Error: m is not in scope.
```

### The `macro_use` attribute

The *`macro_use` attribute* has two purposes. First, it can be used to make a
module's macro scope not end when the module is closed, by applying it to a
module:

```rust
#[macro_use]
mod inner {
    macro_rules! m {
        () => {};
    }
}

m!();
```

Second, it can be used to import macros from another crate, by attaching it to
an `extern crate` declaration appearing in the crate's root module. Macros
imported this way are imported into the prelude of the crate, not textually,
which means that they can be shadowed by any other name. While macros imported
by `#[macro_use]` can be used before the import statement, in case of a
conflict, the last macro imported wins. Optionally, a list of macros to import
can be specified using the [_MetaListIdents_] syntax; this is not supported
when `#[macro_use]` is applied to a module.

<!-- ignore: requires external crates -->
```rust,ignore
#[macro_use(lazy_static)] // Or #[macro_use] to import all macros.
extern crate lazy_static;

lazy_static!{}
// self::lazy_static!{} // Error: lazy_static is not defined in `self`
```

Macros to be imported with `#[macro_use]` must be exported with
`#[macro_export]`, which is described below.

### Path-Based Scope

By default, a macro has no path-based scope. However, if it has the
`#[macro_export]` attribute, then it is declared in the crate root scope and can
be referred to normally as such:

```rust
self::m!();
m!(); // OK: Path-based lookup finds m in the current module.

mod inner {
    super::m!();
    crate::m!();
}

mod mac {
    #[macro_export]
    macro_rules! m {
        () => {};
    }
}
```

Macros labeled with `#[macro_export]` are always `pub` and can be referred to
by other crates, either by path or by `#[macro_use]` as described above.

## Hygiene

By default, all identifiers referred to in a macro are expanded as-is, and are
looked up at the macro's invocation site. This can lead to issues if a macro
refers to an item or macro which isn't in scope at the invocation site. To
alleviate this, the `$crate` metavariable can be used at the start of a path to
force lookup to occur inside the crate defining the macro.

<!-- ignore: requires external crates -->
```rust,ignore
//// Definitions in the `helper_macro` crate.
#[macro_export]
macro_rules! helped {
    // () => { helper!() } // This might lead to an error due to 'helper' not being in scope.
    () => { $crate::helper!() }
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// Usage in another crate.
// Note that `helper_macro::helper` is not imported!
use helper_macro::helped;

fn unit() {
    helped!();
}
```

Note that, because `$crate` refers to the current crate, it must be used with a
fully qualified module path when referring to non-macro items:

```rust
pub mod inner {
    #[macro_export]
    macro_rules! call_foo {
        () => { $crate::inner::foo() };
    }

    pub fn foo() {}
}
```

Additionally, even though `$crate` allows a macro to refer to items within its
own crate when expanding, its use has no effect on visibility. An item or macro
referred to must still be visible from the invocation site. In the following
example, any attempt to invoke `call_foo!()` from outside its crate will fail
because `foo()` is not public.

```rust
#[macro_export]
macro_rules! call_foo {
    () => { $crate::foo() };
}

fn foo() {}
```

> **Version & Edition Differences**: Prior to Rust 1.30, `$crate` and
> `local_inner_macros` (below) were unsupported. They were added alongside
> path-based imports of macros (described above), to ensure that helper macros
> did not need to be manually imported by users of a macro-exporting crate.
> Crates written for earlier versions of Rust that use helper macros need to be
> modified to use `$crate` or `local_inner_macros` to work well with path-based
> imports.

When a macro is exported, the `#[macro_export]` attribute can have the
`local_inner_macros` keyword added to automatically prefix all contained macro
invocations with `$crate::`. This is intended primarily as a tool to migrate
code written before `$crate` was added to the language to work with Rust 2018's
path-based imports of macros. Its use is discouraged in new code.

```rust
#[macro_export(local_inner_macros)]
macro_rules! helped {
    () => { helper!() } // Automatically converted to $crate::helper!().
}

#[macro_export]
macro_rules! helper {
    () => { () }
}
```

## Follow-set Ambiguity Restrictions

The parser used by the macro system is reasonably powerful, but it is limited in
order to prevent ambiguity in current or future versions of the language. In
particular, in addition to the rule about ambiguous expansions, a nonterminal
matched by a metavariable must be followed by a token which has been decided can
be safely used after that kind of match.

As an example, a macro matcher like `$i:expr [ , ]` could in theory be accepted
in Rust today, since `[,]` cannot be part of a legal expression and therefore
the parse would always be unambiguous. However, because `[` can start trailing
expressions, `[` is not a character which can safely be ruled out as coming
after an expression. If `[,]` were accepted in a later version of Rust, this
matcher would become ambiguous or would misparse, breaking working code.
Matchers like `$i:expr,` or `$i:expr;` would be legal, however, because `,` and
`;` are legal expression separators. The specific rules are:

  * `expr` and `stmt` may only be followed by one of: `=>`, `,`, or `;`.
  * `pat` may only be followed by one of: `=>`, `,`, `=`, `|`, `if`, or `in`.
  * `path` and `ty` may only be followed by one of: `=>`, `,`, `=`, `|`, `;`,
    `:`, `>`, `>>`, `[`, `{`, `as`, `where`, or a macro variable of `block`
    fragment specifier.
  * `vis` may only be followed by one of: `,`, an identifier other than a
    non-raw `priv`, any token that can begin a type, or a metavariable with a
    `ident`, `ty`, or `path` fragment specifier.
  * All other fragment specifiers have no restrictions.

When repetitions are involved, then the rules apply to every possible number of
expansions, taking separators into account. This means:

  * If the repetition includes a separator, that separator must be able to
    follow the contents of the repetition.
  * If the repetition can repeat multiple times (`*` or `+`), then the contents
    must be able to follow themselves.
  * The contents of the repetition must be able to follow whatever comes
    before, and whatever comes after must be able to follow the contents of the
    repetition.
  * If the repetition can match zero times (`*` or `?`), then whatever comes
    after must be able to follow whatever comes before.


For more detail, see the [formal specification].

[Hygiene]: #hygiene
[IDENTIFIER]: identifiers.md
[IDENTIFIER_OR_KEYWORD]: identifiers.md
[LIFETIME_TOKEN]: tokens.md#lifetimes-and-loop-labels
[Metavariables]: #metavariables
[Repetitions]: #repetitions
[_Attr_]: attributes.md
[_BlockExpression_]: expressions/block-expr.md
[_DelimTokenTree_]: macros.md
[_Expression_]: expressions.md
[_Item_]: items.md
[_LiteralExpression_]: expressions/literal-expr.md
[_MetaListIdents_]: attributes.md#meta-item-attribute-syntax
[_Pattern_]: patterns.md
[_Statement_]: statements.md
[_TokenTree_]: macros.md#macro-invocation
[_Token_]: tokens.md
[_TypePath_]: paths.md#paths-in-types
[_Type_]: types.md#type-expressions
[_Visibility_]: visibility-and-privacy.md
[formal specification]: macro-ambiguity.md
[token]: tokens.md
