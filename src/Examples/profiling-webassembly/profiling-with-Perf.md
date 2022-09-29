# 在 Linux 中使用 perf

Wasmtime 支持的一个分析器是 Linux 的 [`perf` 分析器](https://perf.wiki.kernel.org/index.php/Main_Page)。这是一个非常强大的分析器，在 Web 上有很多文档，但是在本节的其余部分中，我们假设你正在运行 Linux 并且已经安装了 `perf`。

使用 `perf` 的分析支持使用 `perf` CLI 中的“jitdump”支持。这需要 Wasmtime 本身的运行时支持，因此需要手动更改一些内容，以便在应用程序中启用分析支持。首先，你需要确保使用 `jitdump` Cargo 特性编译 Wasmtime (默认情况下启用该特性)。否则，启用运行时支持取决于如何使用 Wasmtime:
