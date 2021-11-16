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

## LLVM编译命令

我使用的是Windows 11系统，Visual Studio 2022编译环境。需要安装Ninja和Python3。

复制llvm-project仓库，先mkdir build，cd build，然后执行以下的命令。我指定了安装目录，需要根据自己的系统情况来更改。

```
cmake 
   -G "Ninja"  
   -S ..\llvm 
   -D LLVM_INSTALL_UTILS=ON  
   -D LLVM_ENABLE_PROJECTS="clang;lld;compiler-rt;mlir;libc;libcxx"  
   -D LLVM_TARGETS_TO_BUILD="all" 
   -D LLVM_EXPERIMENTAL_TARGETS_TO_BUILD="M68k;AVR;CSKY"  
   -D CMAKE_BUILD_TYPE=Release  
   -D CMAKE_INSTALL_PREFIX=D:\Applications\LLVM 
   -D LLVM_HOST_TRIPLE=x86_64-pc-windows-msvc
   -DLLVM_USE_CRT_RELEASE=MT 
```

运行ninja来编译，这时需要等待若干分钟。然后运行ninja install来安装。

这之后编译rustc需要给定config.toml文件。我使用的文件如下。我直接使用了已经安装在电脑上的llvm编译版，并没有再次复制llvm-project子模块。

```toml
# Includes one of the default files in src/bootstrap/defaults
profile = "codegen"
changelog-seen = 2

[target.x86_64-pc-windows-msvc]
llvm-config = "D:/Applications/LLVM/bin/llvm-config"
crt-static = true
```

使用`python x.py build`来编译。

得到了stage1 rustc编译包，为了使用方便，可以把它连接到rustup上。

```
rustup toolchain link stage1 .\build\x86_64-pc-windows-msvc\stage1
```

连接之后，打印支持的目标，就能找到csky了。

```
PS > rustc +stage1 --print target-list
... 省略若干行 ...
csky-unknown-none-elf
```

愉快地和CSky + Rust玩耍吧！

## 版权

目前还没法儿编译core包，所以项目模拟了core包需要的所有语言项，参考了[mini_core example](https://github.com/rust-lang/rust/blob/3aedcf06b73fc36feeebca3d579e1d2a6c40acc5/compiler/rustc_codegen_cranelift/example/mini_core.rs)的代码。
