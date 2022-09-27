# Markdown 解析器

以下步骤描述了使用 [pulldown-cmark](https://github.com/raphlinus/pulldown-cmark) 在 Rust 中实现 WASI markdown 解析器。

首先，我们将使用 cargo 生成一个新的可执行文件：

```shell
cargo new --bin rust_wasi_markdown_parser
cd rust_wasi_markdown_parser
```

然后，我们将打开 `src/main.rs` 并输入以下内容。 请查看注释以了解程序将做些什么。

## src/main.rs

```rust
// Import our CLI parsing libraries (And PathBuf for reading paths)
extern crate structopt;

use structopt::StructOpt;
use std::path::PathBuf;

// Import our markdown parser library, crate
extern crate pulldown_cmark;

use pulldown_cmark::{html, Parser};

// Import from the standard library, to allow reading from the file system
use std::fs;

// Define our CLI options using structopt
#[derive(StructOpt)]
#[structopt(name = "rust_wasi_markdown_parser", about = "Markdown to HTML renderer CLI, written with Rust & WASI")]
pub struct Options {
    /// The markdown file to render
    #[structopt(parse(from_os_str))]
    filename: PathBuf,
}

// Our entrypoint into our WASI module
fn main() {

    // Get the passed CLI options
    let options = Options::from_args();

    // Read the markdown file into a string
    let contents = fs::read_to_string(options.filename)
        .expect("Something went wrong reading the file");

    // Run our parsing function to get back an HTML string
    let result = render_markdown(contents);

    // Print out the resulting HTML to standard out
    println!("{}", result);
}

pub fn render_markdown(markdown: String) -> String {
    let mut html_buf = String::new();
    let parser = Parser::new(&markdown[..]);
    html::push_html(&mut html_buf, parser);
    html_buf
}
```

接下来，我们将要添加 WASI 作为可编译的目标。 我们将要求 rustup 工具安装对 WASI 的支持。 然后，把程序编译成 WASI。 为此，将运行：

```shell
rustup target add wasm32-wasi
cargo build --target wasm32-wasi
```

我们的 wasm 文件应该编译为 `target/wasm32-wasi/debug/rust_wasi_markdown_parser.wasm`。 值得注意的是，即使没有直接使用 WASI API，当我们编译程序以 WASI 为目标时，rust API 和标准库也会在后台使用这些 WASI API！ 现在我们已经将程序编译为目标 WASI，让我们运行程序！

为此，我们可以使用 Wasmtime CLI。 但是，关于 Wasmtime、WASI 和基于能力的安全模型，有一点需要注意。 我们需要让程序显式访问设备上的读取文件。 实现 WASI 的 Wasm 模块将不具备此功能，除非我们赋予它们该功能。

要授予使用 Wasmtime CLI 读取目录的能力，我们需要使用 --dir 标志。 --dir 将指示 wasmtime 使传递的目录可用于访问文件。 （也可以 `--mapdir GUEST_DIRECTORY::HOST_DIRECTORY` 使其在内容内的不同路径下可用。）例如：

```shell
wasmtime --dir . my-wasi-program.wasm
```

对于这个例子，我们将向程序传递一个名为 `example_markdown.md` 的 markdown 文件，该文件将存在于我们当前的目录 (`./`) 中。 我们的 markdown 文件 `example_markdown.md` 将包含：

```md
# Hello!

I am example markdown for this demo!
```

因此，要运行我们编译的 WASI 程序，将运行：

```shell
wasmtime --dir . target/wasm32-wasi/debug/rust_wasi_markdown_parser.wasm -- ./example_markdown.md
```

应该如下所示：

```html
<h1>Hello!</h1>
<p>I am example markdown for this demo!</p>
```

万岁！ 我们能够编写一个 Wasm 模块，它使用 WASI 读取 markdown 文件，解析 markdown，并将输出写入标准输出！ 继续阅读以查看更多使用 Wasmtime 从 CLI 甚至嵌入到你的应用程序中执行 Wasm 模块的示例！
