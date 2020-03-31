# 介绍

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

本书无需按章节顺序阅读。每章节都可以独立阅读，但会交叉连接到其他章节，以了解所提到的 Rust 语言的各方面内容——但不详述。

阅读本书有两种主要方式。

第一，寻找特定问题的答案。如果你知道回答问题的章节，你可以直接从目录跳入章节进行阅读。否则，你可以按 `s` 键或单击顶部栏上的放大镜来搜索与问题相关的关键字（译者注：暂不能搜索非英文）。例如：假设你想知道 `let` 语句创建的临时值什么时候会被删除；同时，假设你还不知道[表达式][expressions chapter]一章中定义了[临时对象的生命周期][lifetime of temporaries]。那么，你可以搜索 `temporary let`，第一个搜索结果将带你阅读该部分。 

第二，概括地提高你对 Rust 语言某一方面的认知。这种情况下，只要你浏览目录时看到了想了解的内容，就去开始阅读。如果某个链接看起来挺有趣，点击并阅读该部分。

综上所述，本书没有错误的阅读方式，按照你感觉最有帮助的方式去读它吧。

### 约定

像所有技术书籍一样，在信息的展示方面，本书有一些约定。

* 定义术语的语句中，术语写为*斜体*。当术语在其定义章节之外使用时，通常会有一个链接指向术语定义的片段。

  *示例术语*是一个定义术语的示例。

* 编译 crate 的 Rust 语言版本的差异，以**版本差异**开始，记录在块引用中。

  > **版本差异**：此语法 2015 版本有效，2018 版本起不允许使用。

* 有关书籍状态的，或者大部分超出本书范围的有用资料，以**注**开始，记录在块引用中。

  > **注**：这是一个注释示例。

* 有关对语言的不健全行为，或者易于混淆的语言特性的警告，记录在特殊的警告框里。

  <div class="warning">

  警告：示例警告。

  </div>

* 代码片段内联在 `<code>` 标签里。

  较长的示例代码放在语法高亮的代码框内，其右上角有用于复制、运行和显示隐藏行的控件。

  ```rust
  # // 这是隐藏行
  fn main() {
      println!("这是示例代码");
  }
  ```

* 语法和词法结构，放在块引用中，第一行为粗体上标 <sup>**Lexer**</sup> 或 <sup>**Syntax**</sup>。

  > **<sup>Syntax</sup>**\
  > _语法示例_：\
  > &nbsp;&nbsp; &nbsp;&nbsp; `~` [_Expression_]\
  > &nbsp;&nbsp; | `box` [_Expression_]

  查阅[标记法][Notation]以获取更多细节。

## 做贡献

我们欢迎各类贡献。

欢迎 fork [Rust 参考手册中文翻译仓库]，欢迎提交问题，欢迎发送 PR。

[Rust 程序设计语言]: https://rust-lang.budshome.com
[github issues]: https://github.com/rust-lang/reference/issues
[标准库]: https://doc.rust-lang.org/std
[Rust 参考手册中文翻译仓库]: https://github.com/zzy/rust-reference-zh
[Unstable Book]: https://doc.rust-lang.org/nightly/unstable-book/
[_Expression_]: expressions.md
[cargo book]: https://cargo.budshome.com
[cargo reference]: https://cargo.budshome.com/reference
[expressions chapter]: expressions.md
[lifetime of temporaries]: expressions.md#temporary-lifetimes
[linkage]: linkage.md
[rustc book]: https://doc.rust-lang.org/nightly/rustc/index.html
[Notation]: notation.md
