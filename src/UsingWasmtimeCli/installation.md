# 安装 wasmtime

这里我们将向你展示如何安装 `wasmtime` 命令行工具。注意，这不同于将 Wasmtime 项目嵌入到另一个项目中，如果你想使用wasmtime嵌入到另一个项目中，你需要查阅[嵌入文档](#安装-wasmtime)。

安装 `wasmtime` CLI 工具的最简单方法是通过我们的安装脚本。Linux 和 macOS 用户可以执行以下操作:

```shell
curl https://wasmtime.dev/install.sh -sSf | bash
```

这将下载一个预编译版本的 `wasmtime` 并将其放置在 `$HOME/.wasmtime`，并更新你的 shell 配置以在 `PATH` 中放置正确的目录。

Windows 用户将希望访问我们的[发布页面](https://github.com/bytecodealliance/wasmtime/releases)，并可以下载 MSI 安装程序(`wasmtime-dev-x86_64-windows.msi`) ，并使用它来安装。

你可以通过执行以下命令来确认安装工作:

```shell
$ wasmtime -V
wasmtime-cli 2.0.0
```

## 下载预编译的二进制文件

如果你不想使用安装脚本，或者你可能正在编排 CI 中的某些内容，也可以下载我们预编译的 `wasmtime` 二进制文件。我们现在有两个发布预编译二进制文件的渠道:

1. 每个标记的发行版在 [GitHub 发行版页面](https://github.com/bytecodealliance/wasmtime/releases)上都有一套完整的发行版构件。

2. [开发版本](https://github.com/bytecodealliance/wasmtime/releases/tag/dev)也随着主分支的最新版本不断更新。如果你想要最新和最好的，不介意一点不稳定，这就是你的发布版。

在下载二进制文件时，你可能需要以下归档文件之一(用于开发版本)

- Linux users - \[`wasmtime-dev-x86_64-linux.tar.xz`]
- macOS users - \[`wasmtime-dev-x86_64-macos.tar.xz`]
- Windows users - \[`wasmtime-dev-x86_64-windows.zip`]

这些归档文件中的每一个都有一个 `wasmtime` 二进制文件，可以像 CLI 那样正常执行。

## 从源码中编译

如果你希望从源代码编译 `wasmtime` CLI，那么可以参考[构建的贡献文档](#下载预编译的二进制文件)。如果你对基准测试很好奇，那么一定要使用 `--release` 构建！
