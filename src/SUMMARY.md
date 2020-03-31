# Rust 参考手册

<!--
> [SUMMARY.md](https://github.com/rust-lang/reference/blob/master/src/SUMMARY.md)
> <br />
> commit 363a64a939bf246d7373776826e14065e912131f
-->

[介绍](introduction.md)

- [标记法](notation.md)

- [词法结构](lexical-structure.md)
    - [输入格式](input-format.md)
    - [关键字](keywords.md)
    - [标识符](identifiers.md)
    - [注释](comments.md)
    - [空白](whitespace.md)
    - [记号](tokens.md)
    - [路径](paths.md)

- [宏](macros.md)
    - [声明宏](macros-by-example.md)
    - [过程宏](procedural-macros.md)

- [crate 和源文件](crates-and-source-files.md)

- [条件编译](conditional-compilation.md)

- [项](items.md)
    - [模块](items/modules.md)
    - [extern crate 声明](items/extern-crates.md)
    - [use 声明](items/use-declarations.md)
    - [函数](items/functions.md)
    - [类型别名](items/type-aliases.md)
    - [结构体](items/structs.md)
    - [枚举](items/enumerations.md)
    - [联合体](items/unions.md)
    - [常量项](items/constant-items.md)
    - [静态项](items/static-items.md)
    - [trait](items/traits.md)
    - [实现](items/implementations.md)
    - [外部块](items/external-blocks.md)
    - [泛型：类型和生命周期参数](items/generics.md)
    - [关联项](items/associated-items.md)
    - [可见性和私有性](visibility-and-privacy.md)

- [属性](attributes.md)
    - [测试属性](attributes/testing.md)
    - [派生属性](attributes/derive.md)
    - [诊断属性](attributes/diagnostics.md)
    - [代码生成属性](attributes/codegen.md)
    - [limit 属性](attributes/limits.md)
    - [类型系统属性](attributes/type_system.md)

- [语句和表达式](statements-and-expressions.md)
    - [语句](statements.md)
    - [表达式](expressions.md)
        - [字面量表达式](expressions/literal-expr.md)
        - [路径表达式](expressions/path-expr.md)
        - [块表达式](expressions/block-expr.md)
        - [运算符表达式](expressions/operator-expr.md)
        - [组合表达式](expressions/grouped-expr.md)
        - [数组和索引表达式](expressions/array-expr.md)
        - [元组和索引表达式](expressions/tuple-expr.md)
        - [结构体表达式](expressions/struct-expr.md)
        - [枚举变量表达式](expressions/enum-variant-expr.md)
        - [调用表达式](expressions/call-expr.md)
        - [方法调用表达式](expressions/method-call-expr.md)
        - [字段存取表达式](expressions/field-expr.md)
        - [闭包表达式](expressions/closure-expr.md)
        - [循环表达式](expressions/loop-expr.md)
        - [区间表达式](expressions/range-expr.md)
        - [if 和 if let 表达式](expressions/if-expr.md)
        - [match 表达式](expressions/match-expr.md)
        - [return 表达式](expressions/return-expr.md)
        - [await 表达式](expressions/await-expr.md)

- [模式](patterns.md)

- [类型系统](type-system.md)
    - [类型](types.md)
        - [布尔型](types/boolean.md)
        - [数值型](types/numeric.md)
        - [字符型](types/textual.md)
        - [never 型](types/never.md)
        - [元组](types/tuple.md)
        - [数组](types/array.md)
        - [切片](types/slice.md)
        - [结构体](types/struct.md)
        - [枚举](types/enum.md)
        - [union 型](types/union.md)
        - [函数项](types/function-item.md)
        - [闭包](types/closure.md)
        - [指针](types/pointer.md)
        - [函数指针](types/function-pointer.md)
        - [trait 对象](types/trait-object.md)
        - [impl trait](types/impl-trait.md)
        - [类型参数](types/parameters.md)
        - [推导型](types/inferred.md)
    - [DST](dynamically-sized-types.md)
    - [类型设计](type-layout.md)
    - [内部可变性](interior-mutability.md)
    - [子类型和变型](subtyping.md)
    - [trait 及其生命周期约束](trait-bounds.md)
    - [类型强制转换](type-coercions.md)
    - [析构函数](destructors.md)
    - [生命周期省略](lifetime-elision.md)

- [特殊类型及其 trait](special-types-and-traits.md)

- [内存模型](memory-model.md)
    - [内存分配和其生命周期](memory-allocation-and-lifetime.md)
    - [内存所有权](memory-ownership.md)
    - [变量](variables.md)

- [链接](linkage.md)

- [不安全性](unsafety.md)
    - [不安全函数](unsafe-functions.md)
    - [不安全块](unsafe-blocks.md)
    - [未定义行为](behavior-considered-undefined.md)
    - [不安全行为](behavior-not-considered-unsafe.md)

- [常量计算](const_eval.md)

- [ABI](abi.md)

- [Rust 运行时](runtime.md)

- [附录](appendices.md)
    - [宏定义规范](macro-ambiguity.md)
    - [受其它语言的影响](influences.md)
    - [术语](glossary.md)


