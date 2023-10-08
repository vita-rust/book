# Introduction

Welcome to the Rust on Sony PlayStation Vita Book: An introductory book about using the Rust Programming Language on Sony PlayStation Vita.

Here you can find guidelines on how to start making applications for Sony PlayStation Vita in Rust with std using open source [Vita SDK] homebrew toolchain.

## Considerations

Though it's technically possible to run nostd applications written in pure Rust on Vita (without any additional toolchains), this book focuses on running applications with std. Rust std for Vita target is implemented via [newlib], and relies on the [Vita SDK] toolchain - patched `gcc` compiler, patched `binutils`, as well as a number of CLI tools that allow converting an armv7 elf into an artifact runnable on Vita. 


[Vita SDK]: https://vitasdk.org/
[newlib]: https://github.com/vitasdk/newlib
