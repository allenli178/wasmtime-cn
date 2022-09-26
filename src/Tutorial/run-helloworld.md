# 使用 Wasmtime 运行 hello-world.wasm

## 安装 Wasmtime

Wasmtime CLI 可以通过一个小的安装脚本安装在 Linux 和 macOS 上：

```shell
curl https://wasmtime.dev/install.sh -sSf | bash
```

你可以在 [CLI 安装部分](#使用-wasmtime-运行-hello-worldwasm)找到有关安装 Wasmtime CLI 的更多信息。

## 运行 hello-world.wasm

有多种方法可以使用 Wasmtime 运行 `.wasm` 文件。 在本教程中，我们将使用 CLI，Wasmtime 也可以嵌入到应用程序中。 有关这方面的更多信息，请参阅[嵌入 Wasmtime 部分](./index.md)。

如果你已经构建了 `hello-world.wasm` 文件（执行此操作的说明在[上一节](./create-helloworld.md)中），你可以使用 Wasmtime 从命令行运行它，如下所示：

```shell
wasmtime target/wasm32-wasi/debug/hello-world.wasm
```
