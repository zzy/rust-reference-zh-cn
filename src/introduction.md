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

## *参考手册*并非——

本书不充当 Rust 语言入门，假设你已熟悉 Rust 语言基础。若你需要熟悉语言基础，请阅读 [Rust 程序设计语言]。

This book also does not serve as a reference to the [standard library]
included in the language distribution. Those libraries are documented
separately by extracting documentation attributes from their source code. Many
of the features that one might expect to be language features are library
features in Rust, so what you're looking for may be there, not here.

Similarly, this book does not usually document the specifics of `rustc` as a
tool or of Cargo. `rustc` has its own [book][rustc book]. Cargo has a
[book][cargo book] that contains a [reference][cargo reference]. There are a few
pages such as [linkage] that still describe how `rustc` works.

This book also only serves as a reference to what is available in stable
Rust. For unstable features being worked on, see the [Unstable Book].

Finally, this book is not normative. It may include details that are
specific to `rustc` itself, and should not be taken as a specification for
the Rust language. We intend to produce such a book someday, and until then,
the reference is the closest thing we have to one.

## How to Use This Book

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
[standard library]: ../std/index.html
[the Rust Reference repository]: https://github.com/rust-lang/reference/
[Unstable Book]: https://doc.rust-lang.org/nightly/unstable-book/
[_Expression_]: expressions.md
[cargo book]: ../cargo/index.html
[cargo reference]: ../cargo/reference/index.html
[expressions chapter]: expressions.md
[lifetime of temporaries]: expressions.md#temporary-lifetimes
[linkage]: linkage.md
[rustc book]: ../rustc/index.html
[Notation]: notation.md
[Discord]: https://discord.gg/rust-lang
