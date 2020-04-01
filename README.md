# Rust 参考手册

本书是 Rust 编程语言的主要参考，电子档发布在 https://rust-reference.budshome.com

此文档并非 Rust 语言规范：可能包含特定于 `rustc` 自身的细节，因此不应作为 Rust 语言规范。Rust 语言开发团队计划在未来拿出规范文件，但目前只有本文档所述。

## 状态

- 对照源码位置：https://github.com/rust-lang/reference/tree/master/src
- 每章翻译开头都带有官方链接和 commit hash，若发现与官方不一致，欢迎 Issue 或 PR :bug:

## 依赖

- rustc （Rust 编译器）；
- mdBook （安装命令：`cargo install mdbook`,若需要请[阅读 mdBook 中文文档](https://mdbook.budshome.com))。

## 构建

首先，克隆本仓库，并进入文件夹：

``` Bash
git clone https://github.com/zzy/rust-reference-zh.git
cd rust-reference-zh
```

其次，测试代码，并捕获编译错误：

``` Bash
mdbook test
```

最后，生成电子书：

``` Bash
mdbook build
```

默认生成 `HTML` 文档，存放在 `docs` 文件夹中。

## 贡献者 ✨

<table>
  <tr>
    <td align="center"><a href="https://budshome.com"><img src="src/imgs/contributors/zzy.jpg" width="100px;" alt=""/><br /><sub><b>Zhang Zhongyu</b></sub></a></td>
    <td align="center"><a href="https://github.com/zzy/rust-reference-zh"><img src="src/imgs/contributors/anonymous.jpg" width="100px;" alt=""/><br /><sub><b>欢迎您</b></sub></a></td>
    <td align="center"><a href="https://github.com/zzy/rust-reference-zh"><img src="src/imgs/contributors/anonymous.jpg" width="100px;" alt=""/><br /><sub><b>欢迎您</b></sub></a></td>
    <td align="center"><a href="https://github.com/zzy/rust-reference-zh"><img src="src/imgs/contributors/anonymous.jpg" width="100px;" alt=""/><br /><sub><b>欢迎您</b></sub></a></td>
    <td align="center"><a href="https://github.com/zzy/rust-reference-zh"><img src="src/imgs/contributors/anonymous.jpg" width="100px;" alt=""/><br /><sub><b>欢迎您</b></sub></a></td>
    <td align="center"><a href="https://github.com/zzy/rust-reference-zh"><img src="src/imgs/contributors/anonymous.jpg" width="100px;" alt=""/><br /><sub><b>欢迎您</b></sub></a></td>
  </tr>
</table>

## 做贡献

我们欢迎各类贡献。

欢迎 fork[《Rust 参考手册》中文翻译仓库]，欢迎提交问题，欢迎发送 PR。

[《Rust 参考手册》中文翻译仓库]: https://github.com/zzy/rust-reference-zh
