# 创建 hello-world.wasm

有多种方法可以创建 .wasm 文件，但出于本教程的目的，我们将使用 Rust 工具链。 你可以在[编写 WebAssembly](#创建-hello-worldwasm)章节中找到有关从其他语言创建 .wasm 文件的更多信息。

要使用 Rust 构建 WebAssembly 二进制文件，你需要标准的 Rust 工具链。

[按照这些说明安装 `rustc`、`rustup` 和 `cargo`](https://www.rust-lang.org/tools/install)

下一步，你应该添加 WebAssembly 作为 cargo 的构建目标，如下所示：

```shell
rustup target add wasm32-wasi
```

最后，创建一个名为“hello-world”的新 Rust 项目。 你可以通过运行：

```shell
cargo new hello-world
```

之后，hello-world 文件夹应如下所示。

```shell
hello-world/
├── Cargo.lock
├── Cargo.toml
└── src
   └── main.rs
```

`src` 文件夹中的 `main.rs` 文件应该包含以下 rust 代码。

```rust
fn main() {
    println!("Hello, world!");
}
```

我们可以告诉 `cargo` 构建一个 WebAssembly 文件：

```shell
cargo build --target wasm32-wasi
```

现在，在目标文件夹中，有一个 `hello-world.wasm` 文件。 你可以在这里找到它：

```shell
hello-world/
├── Cargo.lock
├── Cargo.toml
├── src
└── target
   └── ...
   └── wasm32-wasi
      └── debug
         └── ...
         └── hello-world.wasm
```
