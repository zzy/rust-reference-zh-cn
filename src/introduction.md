# Introduction

> [introduction.md](https://github.com/rust-lang/reference/blob/master/src/introduction.md)
> <br />
> commit 1995d18b04a59368f4a91c600876ad987521d833

本书是 Rust 编程语言的主要参考，提供了3类资料：

  - 一些章节非正式地介绍了每种语言结构及其用法。
  - 一些章节非正式地介绍了内存模型、并发模型、运行时服务、链接模型，以及调试工具。
  - 附录章节提供了影响设计的 Rust 语言基础原理和其参考资料。

<div class="warning">

警告：此书暂未完成，每篇参考的记录都需花一些时间。有关本书未记录的内容，请查阅 [GitHub issues]。

</div>

## *参考手册* 并非——

本书并非 Rust 语言入门，阅读它需要有一定的 Rust 语言基础。若你需要学习语言基础，请阅读 [Rust 程序设计语言]。

本书也非 Rust 语言发行版所包含的[标准库]的参考资料，[标准库]文档来源于从源码中抽取的文档属性。许多你期待是 Rust 语言特性的部分，可能属于 [Rust 标准库][标准库]特性。所以你应该在库文档中查阅，而非此书。

类似地，本书通常不能作为记录 `rustc` 或者 `Cargo` 细节的工具书。`rustc` 有自己的[书籍][rustc book]，`Cargo` 有一本包含[参考资料][cargo reference]的[书籍][cargo book]。本书中，包含一些诸如[链接][linkage]的章节，介绍了 `rustc` 是如何工作的。

本书也仅作为 Rust 稳定版的参考资料，关于尚在完善的非稳定特性，请阅读[非稳定版书籍][Unstable Book]。

最后一点，本书并非标准。其包含的一些细节是 `rustc` 自身规范，不应作为 Rust 语言规范。我们计划以后写一本关于 Rust 语言规范的书籍，但目前，此参考手册是最接近 Rust 语言规范的资料。

## 如何使用此书

This book does not assume you are reading this book sequentially. Each
chapter generally can be read standalone, but will cross-link to other chapters
for facets of the language they refer to, but do not discuss.

There are two main ways to read this document.

The first is to answer a specific question. If you know which chapter answers
that question, you can jump to that chapter in the table of contents. Otherwise,
you can press `s` or the click the magnifying glass on the top bar to search for
keywords related to your question. For example, say you wanted to know when a
temporary value created in a let statement is dropped. If you didn't already
know that the [lifetime of temporaries] is defined in the [expressions chapter],
you could search "temporary let" and the first search result will take you to
that section.

The second is to generally improve your knowledge of a facet of the language.
In that case, just browse the table of contents until you see something you
want to know more about, and just start reading. If a link looks interesting,
click it, and read about that section.

That said, there is no wrong way to read this book. Read it however you feel
helps you best.

### Conventions

Like all technical books, this book has certain conventions in how it displays
information. These conventions are documented here.

* Statements that define a term contain that term in *italics*. Whenever that
  term is used outside of that chapter, it is usually a link to the section that
  has this definition.

  An *example term* is an example of a term being defined.

* Differences in the language by which edition the crate is compiled under are
  in a blockquote that start with the words "Edition Differences:" in **bold**.

  > **Edition Differences**: In the 2015 edition, this syntax is valid that is
  > disallowed as of the 2018 edition.

* Notes that contain useful information about the state of the book or point out
  useful, but mostly out of scope, information are in blockquotes that start
  with the word "Note:" in **bold**.

  > **Note**: This is an example note.

* Warnings that show unsound behavior in the language or possibly confusing
  interactions of language features are in a special warning box.

  <div class="warning">

  Warning: This is an example warning.

  </div>

* Code snippets inline in the text are inside `<code>` tags.

  Longer code examples are in a syntax highlighted box that has controls for
  copying, executing, and showing hidden lines in the top right corner.

  ```rust
  # // This is a hidden line.
  fn main() {
      println!("This is a code example");
  }
  ```

* The grammar and lexical structure is in blockquotes with either "Lexer" or
  "Syntax" in <sup>**bold superscript**</sup> as the first line.

  > **<sup>Syntax</sup>**\
  > _ExampleGrammar_:\
  > &nbsp;&nbsp; &nbsp;&nbsp; `~` [_Expression_]\
  > &nbsp;&nbsp; | `box` [_Expression_]

  See [Notation] for more detail.

## Contributing

We welcome contributions of all kinds.

You can contribute to this book by opening an issue or sending a pull
request to [the Rust Reference repository]. If this book does not answer
your question, and you think its answer is in scope of it, please do not
hesitate to file an issue or ask about it in the `#docs` channels on
[Discord]. Knowing what people use this book for the most helps direct our
attention to making those sections the best that they can be.

[Rust 程序设计语言]: https://rust-lang.budshome.com
[github issues]: https://github.com/rust-lang/reference/issues
[标准库]: https://doc.rust-lang.org/std
[the Rust Reference repository]: https://github.com/rust-lang/reference/
[Unstable Book]: https://doc.rust-lang.org/nightly/unstable-book/
[_Expression_]: expressions.md
[cargo book]: https://cargo.budshome.com
[cargo reference]: https://cargo.budshome.com/reference
[expressions chapter]: expressions.md
[lifetime of temporaries]: expressions.md#temporary-lifetimes
[linkage]: linkage.md
[rustc book]: https://doc.rust-lang.org/nightly/rustc/index.html
[Notation]: notation.md
[Discord]: https://discord.gg/rust-lang
