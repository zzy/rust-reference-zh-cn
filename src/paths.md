# 路径

> [paths.md](https://github.com/rust-lang/reference/blob/master/src/paths.md)
> <br />
> commit a4cdf6d138a4b369df2ed8f0de61008edf76b56b

_路径_ 是一个或多个由<span class="parenthetical">命名空间限定符（`::`）</span>_逻辑_ 分隔的路径片段序列。如果路径仅由一个路径片段组成，则它引用当前控制域内的[项][item]或[变量][variable]。如果路径包含多个路径片段，则常是引用具体项。

两个仅由标识符部分组成的简单路径的例子：

<!-- ignore: syntax fragment -->
```rust,ignore
x;
x::y::z;
```

## 路径类型

### 简单路径

> **<sup>Syntax</sup>**\
> _SimplePath_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _SimplePathSegment_ (`::` _SimplePathSegment_)<sup>\*</sup>
>
> _SimplePathSegment_ :\
> &nbsp;&nbsp; [IDENTIFIER] | `super` | `self` | `crate` | `$crate`

简单路径常用于[可见性][visibility]标记、[属性][attributes]、[宏][macros]，以及 [`use`] 声明项。例如：

```rust
use std::io::{self, Write};
mod m {
    #[clippy::cyclomatic_complexity = "0"]
    pub (in super) fn f1() {}
}
```

### 表达式路径

> **<sup>Syntax</sup>**\
> _PathInExpression_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _PathExprSegment_ (`::` _PathExprSegment_)<sup>\*</sup>
>
> _PathExprSegment_ :\
> &nbsp;&nbsp; _PathIdentSegment_ (`::` _GenericArgs_)<sup>?</sup>
>
> _PathIdentSegment_ :\
> &nbsp;&nbsp; [IDENTIFIER] | `super` | `self` | `Self` | `crate` | `$crate`
>
> _GenericArgs_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `<` `>`\
> &nbsp;&nbsp; | `<` _GenericArgsLifetimes_ `,`<sup>?</sup> `>`\
> &nbsp;&nbsp; | `<` _GenericArgsTypes_ `,`<sup>?</sup> `>`\
> &nbsp;&nbsp; | `<` _GenericArgsBindings_ `,`<sup>?</sup> `>`\
> &nbsp;&nbsp; | `<` _GenericArgsTypes_ `,` _GenericArgsBindings_ `,`<sup>?</sup> `>`\
> &nbsp;&nbsp; | `<` _GenericArgsLifetimes_ `,` _GenericArgsTypes_ `,`<sup>?</sup> `>`\
> &nbsp;&nbsp; | `<` _GenericArgsLifetimes_ `,` _GenericArgsBindings_ `,`<sup>?</sup> `>`\
> &nbsp;&nbsp; | `<` _GenericArgsLifetimes_ `,` _GenericArgsTypes_ `,` _GenericArgsBindings_ `,`<sup>?</sup> `>`
>
> _GenericArgsLifetimes_ :\
> &nbsp;&nbsp; [_Lifetime_] (`,` [_Lifetime_])<sup>\*</sup>
>
> _GenericArgsTypes_ :\
> &nbsp;&nbsp; [_Type_] (`,` [_Type_])<sup>\*</sup>
>
> _GenericArgsBindings_ :\
> &nbsp;&nbsp; _GenericArgsBinding_ (`,` _GenericArgsBinding_)<sup>\*</sup>
>
> _GenericArgsBinding_ :\
> &nbsp;&nbsp; [IDENTIFIER] `=` [_Type_]

表达式路径允许为路径指定泛型参数，被用在各种[表达式][expressions]和[模式][patterns]中。

记号 `::` 出现在泛型参数开端的尖括号 `<` 之前，以避免小于号的歧义，俗称“涡轮鱼（turbofish）”语法。

```rust
(0..10).collect::<Vec<_>>();
Vec::<u8>::with_capacity(1024);
```

## 限定路径

> **<sup>Syntax</sup>**\
> _QualifiedPathInExpression_ :\
> &nbsp;&nbsp; _QualifiedPathType_ (`::` _PathExprSegment_)<sup>+</sup>
>
> _QualifiedPathType_ :\
> &nbsp;&nbsp; `<` [_Type_] (`as` _TypePath_)? `>`
>
> _QualifiedPathInType_ :\
> &nbsp;&nbsp; _QualifiedPathType_ (`::` _TypePathSegment_)<sup>+</sup>

完全限定的路径允许消除 [trait 实现][trait implementations]的路径歧义和指定[规范路径](#canonical-paths)，在使用时支持类型语法。详述如下：

```rust
struct S;
impl S {
    fn f() { println!("S"); }
}
trait T1 {
    fn f() { println!("T1 f"); }
}
impl T1 for S {}
trait T2 {
    fn f() { println!("T2 f"); }
}
impl T2 for S {}
S::f();  // Calls the inherent impl.
<S as T1>::f();  // Calls the T1 trait function.
<S as T2>::f();  // Calls the T2 trait function.
```

### 类型路径

> **<sup>Syntax</sup>**\
> _TypePath_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _TypePathSegment_ (`::` _TypePathSegment_)<sup>\*</sup>
>
> _TypePathSegment_ :\
> &nbsp;&nbsp; _PathIdentSegment_ `::`<sup>?</sup> ([_GenericArgs_] | _TypePathFn_)<sup>?</sup>
>
> _TypePathFn_ :\
> `(` _TypePathFnInputs_<sup>?</sup> `)` (`->` [_Type_])<sup>?</sup>
>
> _TypePathFnInputs_ :\
> [_Type_] (`,` [_Type_])<sup>\*</sup> `,`<sup>?</sup>

类型路径用于类型定义、trait 约束、类型参数约束，以及限定路径。

尽管在泛型参数之前允许使用记号 `::`，但其不是必需的。因为在类型路径中，没有如同限定路径中 _PathInExpression_ 那样的歧义。

```rust
# mod ops {
#     pub struct Range<T> {f1: T}
#     pub trait Index<T> {}
#     pub struct Example<'a> {f1: &'a i32}
# }
# struct S;
impl ops::Index<ops::Range<usize>> for S { /*...*/ }
fn i<'a>() -> impl Iterator<Item = ops::Example<'a>> {
    // ...
#    const EXAMPLE: Vec<ops::Example<'static>> = Vec::new();
#    EXAMPLE.into_iter()
}
type G = std::boxed::Box<dyn std::ops::FnOnce(isize) -> isize>;
```

## 路径限定符

路径可以通过多种前导限定符来改变其解析的方式。

### `::`

Paths starting with `::` are considered to be global paths where the segments of the path
start being resolved from the crate root. Each identifier in the path must resolve to an
item.

> **Edition Differences**: In the 2015 Edition, the crate root contains a variety of
> different items, including external crates, default crates such as `std` and `core`, and
> items in the top level of the crate (including `use` imports).
>
> Beginning with the 2018 Edition, paths starting with `::` can only reference crates.

```rust
mod a {
    pub fn foo() {}
}
mod b {
    pub fn foo() {
        ::a::foo(); // call `a`'s foo function
        // In Rust 2018, `::a` would be interpreted as the crate `a`.
    }
}
# fn main() {}
```

### `self`

`self` resolves the path relative to the current module. `self` can only be used as the
first segment, without a preceding `::`.

```rust
fn foo() {}
fn bar() {
    self::foo();
}
# fn main() {}
```

### `Self`

`Self`, with a capital "S", is used to refer to the implementing type within
[traits] and [implementations].

`Self` can only be used as the first segment, without a preceding `::`.

```rust
trait T {
    type Item;
    const C: i32;
    // `Self` will be whatever type that implements `T`.
    fn new() -> Self;
    // `Self::Item` will be the type alias in the implementation.
    fn f(&self) -> Self::Item;
}
struct S;
impl T for S {
    type Item = i32;
    const C: i32 = 9;
    fn new() -> Self {           // `Self` is the type `S`.
        S
    }
    fn f(&self) -> Self::Item {  // `Self::Item` is the type `i32`.
        Self::C                  // `Self::C` is the constant value `9`.
    }
}
```

### `super`

`super` in a path resolves to the parent module. It may only be used in leading
segments of the path, possibly after an initial `self` segment.

```rust
mod a {
    pub fn foo() {}
}
mod b {
    pub fn foo() {
        super::a::foo(); // call a's foo function
    }
}
# fn main() {}
```

`super` may be repeated several times after the first `super` or `self` to refer to
ancestor modules.

```rust
mod a {
    fn foo() {}

    mod b {
        mod c {
            fn foo() {
                super::super::foo(); // call a's foo function
                self::super::super::foo(); // call a's foo function
            }
        }
    }
}
# fn main() {}
```

### `crate`

`crate` resolves the path relative to the current crate. `crate` can only be used as the
first segment, without a preceding `::`.

```rust
fn foo() {}
mod a {
    fn bar() {
        crate::foo();
    }
}
# fn main() {}
```

### `$crate`

`$crate` is only used within [macro transcribers], and can only be used as the first
segment, without a preceding `::`. `$crate` will expand to a path to access items from the
top level of the crate where the macro is defined, regardless of which crate the macro is
invoked.

```rust
pub fn increment(x: u32) -> u32 {
    x + 1
}

#[macro_export]
macro_rules! inc {
    ($x:expr) => ( $crate::increment($x) )
}
# fn main() { }
```

## Canonical paths

Items defined in a module or implementation have a *canonical path* that
corresponds to where within its crate it is defined. All other paths to these
items are aliases. The canonical path is defined as a *path prefix* appended by
the path segment the item itself defines.

[Implementations] and [use declarations] do not have canonical paths, although
the items that implementations define do have them. Items defined in
block expressions do not have canonical paths. Items defined in a module that
does not have a canonical path do not have a canonical path. Associated items
defined in an implementation that refers to an item without a canonical path,
e.g. as the implementing type, the trait being implemented, a type parameter or
bound on a type parameter, do not have canonical paths.

The path prefix for modules is the canonical path to that module. For bare
implementations, it is the canonical path of the item being implemented
surrounded by <span class="parenthetical">angle (`<>`)</span> brackets. For
[trait implementations], it is the canonical path of the item being implemented
followed by `as` followed by the canonical path to the trait all surrounded in
<span class="parenthetical">angle (`<>`)</span> brackets.

The canonical path is only meaningful within a given crate. There is no global
namespace across crates; an item's canonical path merely identifies it within
the crate.

```rust
// Comments show the canonical path of the item.

mod a { // ::a
    pub struct Struct; // ::a::Struct

    pub trait Trait { // ::a::Trait
        fn f(&self); // a::Trait::f
    }

    impl Trait for Struct {
        fn f(&self) {} // <::a::Struct as ::a::Trait>::f
    }

    impl Struct {
        fn g(&self) {} // <::a::Struct>::g
    }
}

mod without { // ::without
    fn canonicals() { // ::without::canonicals
        struct OtherStruct; // None

        trait OtherTrait { // None
            fn g(&self); // None
        }

        impl OtherTrait for OtherStruct {
            fn g(&self) {} // None
        }

        impl OtherTrait for ::a::Struct {
            fn g(&self) {} // None
        }

        impl ::a::Trait for OtherStruct {
            fn f(&self) {} // None
        }
    }
}

# fn main() {}
```

[_GenericArgs_]: #paths-in-expressions
[_Lifetime_]: trait-bounds.md
[_Type_]: types.md#type-expressions
[item]: items.md
[variable]: variables.md
[implementations]: items/implementations.md
[use declarations]: items/use-declarations.md
[IDENTIFIER]: identifiers.md
[`use`]: items/use-declarations.md
[attributes]: attributes.md
[expressions]: expressions.md
[macro transcribers]: macros-by-example.md
[macros]: macros-by-example.md
[patterns]: patterns.md
[trait implementations]: items/implementations.md#trait-implementations
[traits]: items/traits.md
[visibility]: visibility-and-privacy.md
