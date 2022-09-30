# wasmtime 的命令行工具选项

`wasmtime` CLI 被组织成几个子命令。如果没有提供子命令，将假设 `run`，即执行一个 wasm 文件。`wasmtime` 支持的子命令如下：

## help

这是一个通用的子命令，用于将帮助信息打印到终端。你可以执行以下任意数字：

```bash
wasmtime help
wasmtime --help
wasmtime -h
wasmtime help run
wasmtime run -h
```

如果有疑问，请尝试运行 `help` 命令以了解更多关于功能的信息！

## run

这是 `wasmtime` CLI 的主子命令，如果没有提供其他子命令，它也是默认命令。`run` 命令将执行 WebAssembly 模块。这意味着模块将被编译成原生代码，实例化，然后可选的执行导出。

`wasmtime` CLI 将自动挂载任何与 WASI 相关的导入功能，但此时如果你的模块导入任何其他功能，它将实例化失败。

`run` 命令接受一个位置参数，该参数是要运行的模块的名称:

```bash
wasmtime run foo.wasm
wasmtime foo.wasm
```

请注意，`wasmtime` CLI 可以同时接受二进制 WebAssembly 文件(`*.wasm`)以及 WebAssembly 的文本格式(`*.wat`):

```bash
wasmtime foo.wat
```

`run` 命令接受一个可选的 `invoke` 参数，该参数是要运行的模块的导出函数的名称。

```bash
wasmtime run foo.wasm --invoke initialize
```

## wast

`wast` 命令执行一个 `*.wast` 文件，它是官方 WebAssembly 规范测试套件的测试格式。这个子命令将执行脚本文件，该文件支持许多指令来实例化模块、链接测试等。

像这样执行:

```bash
wasmtime wast foo.wast
```

## config

此子命令用于控制和编辑本地 wasmtime 配置设置。当前的主要目的是配置 [wasmtime 的代码缓存如何工作](#config)。你可以创建一个新的配置文件，以便使用以下命令进行编辑:

```bash
wasmtime config new
```

然后打印出可以编辑的文件路径。

## compile

此子命令用于 Ahead-Of-Time (AOT)编译 WebAssembly 模块以生成“已编译的 wasm”(.cwasm)文件。

然后，可以使用 `wasmtime run` 子命令来运行 AOT 编译的 WebAssembly 模块:

```bash
wasmtime compile foo.wasm
wasmtime foo.cwasm
```

AOT 编译的模块可以从与 AOT 完成的模块的目标环境兼容的主机上运行。

## settings

此子命令用于打印给定目标的可用 Cranelift 设置。

当没有选项运行时，它将打印主机目标的设置，并显示主机的 Cranelift 设置:

```bash
wasmtime settings
```
