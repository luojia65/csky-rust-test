# csky上运行rust的例子

因为rust主线还没有支持csky指令集，暂时来讲你需要先编译rustc，然后才能使用这个项目。

## 编译rustc

编译rustc你需要链接到rustup stage1上，然后编译一个core包，这样才能编译一个嵌入式应用。如果您惯用嵌入式c语言，core包的地位相当于c语言的libc。请参考rustc的编译教程：[这里](https://rustc-dev-guide.rust-lang.org/building/how-to-build-and-run.html)。

输入以下指令，如果能得到有效的输出，说明rustc编译正确。

```
PS > rustc +stage1 -vV
rustc 1.58.0-dev
binary: rustc
commit-hash: unknown
commit-date: unknown
host: x86_64-pc-windows-msvc
release: 1.58.0-dev
LLVM version: 14.0.0
```

我们需要指定目标为csky-unknown-none-elf，用cargo编译这个项目。

``` 
PS > cargo +stage1 build --target csky-unknown-none-elf
   Compiling csky-rust-test v0.1.0 (D:\CSkyRust\csky-rust-test)
```

这样就能完成编译过程了。
