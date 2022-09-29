# 使用 wasmtime 命令行工具

除了允许将 Wasmtime 作为库使用的嵌入 API 之外，Wasmtime 项目还提供了一个 `wasmtime` CLI 工具，可以方便地从命令行执行 WebAssembly 模块。

本节将提供关于 `wasmtime` CLI 及其包含的主要功能的指南。简而言之，你可以像下面这样执行 WebAssembly 文件(实际上作为 `start` 函数的一部分执行工作):

```shell
wasmtime foo.wasm
```

或者类似地，如果你想调用“start”函数，例如使用 WASI 模块，则可以执行

```shell
wasmtime --invoke _start foo.wasm
```

有关更多信息，请确保查看[如何安装 CLI](#使用-wasmtime-命令行工具)、[可以传递的选项列表](#使用-wasmtime-命令行工具)以及[如何启用日志记录](#使用-wasmtime-命令行工具)。
