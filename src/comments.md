# 注释

> [comments.md](https://github.com/rust-lang/reference/blob/master/src/comments.md)
> <br />
> commit 993393d362cae51584d580f86c4f38d43ae76efc

> **<sup>Lexer</sup>**\
> 行注释：\
> &nbsp;&nbsp; &nbsp;&nbsp; `//` (~[`/` `!`] | `//`) ~`\n`<sup>\*</sup>\
> &nbsp;&nbsp; | `//`
>
> 块注释：\
> &nbsp;&nbsp; &nbsp;&nbsp; `/*` (~[`*` `!`] | `**` | _BlockCommentOrDoc_)
>      (_BlockCommentOrDoc_ | ~`*/`)<sup>\*</sup> `*/`\
> &nbsp;&nbsp; | `/**/`\
> &nbsp;&nbsp; | `/***/`
>
> INNER_LINE_DOC :\
> &nbsp;&nbsp; `//!` ~[`\n` _IsolatedCR_]<sup>\*</sup>
>
> INNER_BLOCK_DOC :\
> &nbsp;&nbsp; `/*!` ( _BlockCommentOrDoc_ | ~[`*/` _IsolatedCR_] )<sup>\*</sup> `*/`
>
> 文档注释：\
> &nbsp;&nbsp; `///` (~`/` ~[`\n` _IsolatedCR_]<sup>\*</sup>)<sup>?</sup>
>
> OUTER_BLOCK_DOC :\
> &nbsp;&nbsp; `/**` (~`*` | _BlockCommentOrDoc_ )
>              (_BlockCommentOrDoc_ | ~[`*/` _IsolatedCR_])<sup>\*</sup> `*/`
>
> _BlockCommentOrDoc_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; BLOCK_COMMENT\
> &nbsp;&nbsp; | OUTER_BLOCK_DOC\
> &nbsp;&nbsp; | INNER_BLOCK_DOC
>
> _IsolatedCR_ :\
> &nbsp;&nbsp; _A `\r` not followed by a `\n`_

## 非文档注释

Rust 代码中的注释大体上遵循 C++ 风格的行（`//`）和块（`/* ... */`）注释形式，嵌套块注释也被支持。

非文档注释被当作空白的形式解析。

## 文档注释

Line doc comments beginning with exactly _three_ slashes (`///`), and block
doc comments (`/** ... */`), both inner doc comments, are interpreted as a
special syntax for [`doc` attributes]. That is, they are equivalent to writing
`#[doc="..."]` around the body of the comment, i.e., `/// Foo` turns into
`#[doc="Foo"]` and `/** Bar */` turns into `#[doc="Bar"]`.

Line comments beginning with `//!` and block comments `/*! ... */` are
doc comments that apply to the parent of the comment, rather than the item
that follows.  That is, they are equivalent to writing `#![doc="..."]` around
the body of the comment. `//!` comments are usually used to document
modules that occupy a source file.

Isolated CRs (`\r`), i.e. not followed by LF (`\n`), are not allowed in doc
comments.

## 示例

```rust
//! A doc comment that applies to the implicit anonymous module of this crate

pub mod outer_module {

    //!  - Inner line doc
    //!! - Still an inner line doc (but with a bang at the beginning)

    /*!  - Inner block doc */
    /*!! - Still an inner block doc (but with a bang at the beginning) */

    //   - Only a comment
    ///  - Outer line doc (exactly 3 slashes)
    //// - Only a comment

    /*   - Only a comment */
    /**  - Outer block doc (exactly) 2 asterisks */
    /*** - Only a comment */

    pub mod inner_module {}

    pub mod nested_comments {
        /* In Rust /* we can /* nest comments */ */ */

        // All three types of block comments can contain or be nested inside
        // any other type:

        /*   /* */  /** */  /*! */  */
        /*!  /* */  /** */  /*! */  */
        /**  /* */  /** */  /*! */  */
        pub mod dummy_item {}
    }

    pub mod degenerate_cases {
        // empty inner line doc
        //!

        // empty inner block doc
        /*!*/

        // empty line comment
        //

        // empty outer line doc
        ///

        // empty block comment
        /**/

        pub mod dummy_item {}

        // empty 2-asterisk block isn't a doc block, it is a block comment
        /***/

    }

    /* The next one isn't allowed because outer doc comments
       require an item that will receive the doc */

    /// Where is my item?
#   mod boo {}
}
```

[`doc` attributes]: ../rustdoc/the-doc-attribute.html
