# 模块
·
> [items/modules.md](https://github.com/rust-lang/reference/blob/master/src/items/modules.md)
> <br />
> commit - f8e76ee9368f498f7f044c719de68c7d95da9972 - 2019-11-08

> **<sup>Syntax:</sup>**\
> _Module_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `mod` [IDENTIFIER] `;`\
> &nbsp;&nbsp; | `mod` [IDENTIFIER] `{`\
> &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [_Item_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; `}`

模块是零个或多个[项][items]的容器。

*模块项* 是一个模块，包含在大括号中，需要命名，并以关键字 `mod` 作为前缀。模块项将一个新的命名模块引入到组成 crate 的模块树中。模块可以任意嵌套。

模块示例：

```rust
mod math {
    type Complex = (f64, f64);
    fn sin(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
    fn cos(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
    fn tan(f: f64) -> f64 {
        /* ... */
#       unimplemented!();
    }
}
```

模块和类型共享相同的命名空间。禁止在作用域中声明与模块同名的命名类型，即：类型定义、trait、结构体、枚举、联合体、类型参数或 crate 不能遮蔽作用域中模块的名称，反之亦然。使用 `use` 纳入作用域的项同样有此限制。

## 模块的源文件名字

从外部文件加载没有主体的模块。当模块没有 `path` 属性时，文件的路径将镜像逻辑[模块路径][module
path]。原型模块路径组件是目录，且模块的内容位于一个具有模块名称和 `.rs` 扩展名的文件中。例如，如下模块结构具有相应的文件系统结构：

模块路径                   | 文件系统路径      | 文件内容
------------------------- | ---------------  | -------------
`crate`                   | `lib.rs`         | `mod util;`
`crate::util`             | `util.rs`        | `mod config;`
`crate::util::config`     | `util/config.rs` |

Module filenames may also be the name of the module as a directory with the
contents in a file named `mod.rs` within that directory. The above example can
alternately be expressed with `crate::util`'s contents in a file named
`util/mod.rs`. It is not allowed to have both `util.rs` and `util/mod.rs`.

> **Note**: Previous to `rustc` 1.30, using `mod.rs` files was the way to load
> a module with nested children. It is encouraged to use the new naming
> convention as it is more consistent, and avoids having many files named
> `mod.rs` within a project.

### The `path` attribute

The directories and files used for loading external file modules can be
influenced with the `path` attribute.

For `path` attributes on modules not inside inline module blocks, the file
path is relative to the directory the source file is located. For example, the
following code snippet would use the paths shown based on where it is located:

<!-- ignore: requires external files -->
```rust,ignore
#[path = "foo.rs"]
mod c;
```

Source File    | `c`'s File Location | `c`'s Module Path
-------------- | ------------------- | ----------------------
`src/a/b.rs`   | `src/a/foo.rs`      | `crate::a::b::c`
`src/a/mod.rs` | `src/a/foo.rs`      | `crate::a::c`

For `path` attributes inside inline module blocks, the relative location of
the file path depends on the kind of source file the `path` attribute is
located in. "mod-rs" source files are root modules (such as `lib.rs` or
`main.rs`) and modules with files named `mod.rs`. "non-mod-rs" source files
are all other module files. Paths for `path` attributes inside inline module
blocks in a mod-rs file are relative to the directory of the mod-rs file
including the inline module components as directories. For non-mod-rs files,
it is the same except the path starts with a directory with the name of the
non-mod-rs module. For example, the following code snippet would use the paths
shown based on where it is located:

<!-- ignore: requires external files -->
```rust,ignore
mod inline {
    #[path = "other.rs"]
    mod inner;
}
```

Source File    | `inner`'s File Location   | `inner`'s Module Path
-------------- | --------------------------| ----------------------------
`src/a/b.rs`   | `src/a/b/inline/other.rs` | `crate::a::b::inline::inner`
`src/a/mod.rs` | `src/a/inline/other.rs`   | `crate::a::inline::inner`

An example of combining the above rules of `path` attributes on inline modules
and nested modules within (applies to both mod-rs and non-mod-rs files):

<!-- ignore: requires external files -->
```rust,ignore
#[path = "thread_files"]
mod thread {
    // Load the `local_data` module from `thread_files/tls.rs` relative to
    // this source file's directory.
    #[path = "tls.rs"]
    mod local_data;
}
```

## Prelude Items

Modules implicitly have some names in scope. These name are to built-in types,
macros imported with [`#[macro_use]`][macro_use] on an extern crate, and by the crate's
[prelude]. These names are all made of a single identifier. These names are not
part of the module, so for example, any name `name`, `self::name` is not a
valid path. The names added by the [prelude] can be removed by placing the
`no_implicit_prelude` [attribute] onto the module or one of its ancestor modules.

## Attributes on Modules

Modules, like all items, accept outer attributes. They also accept inner
attributes: either after `{` for a module with a body, or at the beginning of the
source file, after the optional BOM and shebang.

The built-in attributes that have meaning on a module are [`cfg`],
[`deprecated`], [`doc`], [the lint check attributes], `path`, and
`no_implicit_prelude`. Modules also accept macro attributes.

[_InnerAttribute_]: ../attributes.md
[_Item_]: ../items.md
[macro_use]: ../macros-by-example.md#the-macro_use-attribute
[`cfg`]: ../conditional-compilation.md
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[IDENTIFIER]: ../identifiers.md
[attribute]: ../attributes.md
[items]: ../items.md
[module path]: ../paths.md
[prelude]: ../crates-and-source-files.md#preludes-and-no_std
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
