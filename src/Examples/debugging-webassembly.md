# 调试 Webassembly

以下步骤描述了在 Wasmtime 中调试 WebAssembly 模块的常用方法:

1. 在启用调试信息的情况下编译 WebAssembly，通常是 `-g`; 例如:

    ```shell
    clang foo.c -g -o foo.wasm
    ```

2. 在启用调试信息的情况下运行 Wasmtime; 这是来自 CLI 的`-g` 和嵌入式的 `Config::debug_info(true)`([参见 Rust 嵌入式中的调试](#调试-webassembly))

3. 使用受支持的调试器：

    ```shell
    lldb -- wasmtime run -g foo.wasm
    ```

    ```shell
    gdb --args wasmtime run -g foo.wasm
    ```

如果你遇到麻烦，下面的讨论可能会有所帮助:

- 在 MacOS 上使用 LLDB，你可能需要运行: `settings set plugin.jit-loader.gdb.enable on`([#1953](https://github.com/bytecodealliance/wasmtime/issues/1953))

- 使用 LLDB，调用 `__vmctx.set()`在调用任何解引用操作符之前设置当前上下文([#1482](https://github.com/bytecodealliance/wasmtime/pull/1482)):

```shell
(lldb) p __vmctx->set()
(lldb) p *foo
```

- 实例内存的起始地址可以在 `__vmctx->memory` 中找到
