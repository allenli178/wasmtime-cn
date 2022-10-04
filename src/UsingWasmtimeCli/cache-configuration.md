# wasmtime 的缓存配置

配置文件使用 [toml](https://github.com/toml-lang/toml) 格式。你可以用下面语句在默认位置创建一个配置文件：

```bash
wasmtime config new
```

无论成功与否，它都会打印位置。请参考 `--help` 消息以使用自定义位置。

除 `enabled` 外，所有设置都是可选的。如果未指定该设置，则使用默认值。因此，如果你不知道使用什么值，就不要指定它们。将来可能会调整默认值。

Wasmtime 假设所有选项都在 `cache` 部分中。

配置样例：

```toml
[cache]
enabled = true
directory = "/nfs-share/wasmtime-cache/"
cleanup-interval = "30m"
files-total-size-soft-limit = "1Gi"
```

请参考[缓存系统](#缓存系统)部分了解其工作原理。

如果你认为应该对某些默认值进行调优，应该引入一些新的设置，或者应该更改某些行为，那么欢迎你对此进行讨论并贡献给 [Wasmtime 代码仓库](https://github.com/bytecodealliance/wasmtime)。

## 设置 enabled

- **类型**：boolean
- **格式**：`true | false`
- **默认**：`true`

指定是否使用缓存系统。

这个字段是强制性的。当未指定配置文件且默认位置不存在任何配置文件时，将使用默认值。

## 设置 directory

- **类型**：string(path)
- **默认**：在[目录](https://crates.io/crates/directories) crate 中查找 `cache_dir`

指定缓存目录的位置。必须是绝对路径。

## 设置 worker-event-queue-size

- **类型**：string(SI prefix)
- **格式**：`"{integer}(K | M | G | T | P)?"`
- **默认**：`"16"`

[缓存工作线程](#缓存工作线程)事件队列的大小。如果队列已满，传入的缓存使用事件将被删除。

## 设置 baseline-compression-level

- **类型**：integer
- **默认**：`3`，默认的 zstd 压缩级别

[缓存系统](#缓存系统)写入新缓存文件时使用的压缩级别。Wasmtime 使用 [zstd](https://facebook.github.io/zstd/) 压缩。

## 设置 optimized-compression-level

- **类型**：integer
- **默认**：`20`

当[缓存工作线程](#缓存工作线程)决定重新压缩缓存文件时使用的压缩级别。Wasmtime 使用 [zstd](https://facebook.github.io/zstd/) 压缩。

## 设置 optimized-compression-usage-counter-threshold

- **类型**：string(SI prefix)
- **格式**：`"{integer}(K | M | G | T | P)?"`
- **默认**：`"256"`

[缓存工作线程](#缓存工作线程)重新压缩缓存文件的条件之一是文件的使用次数超过这个阈值。

## 设置 cleanup-interval

- **类型**：string(duration)
- **格式**：`"{integer}(s | m | h | d )"`
- **默认**：`"1h"`

当[缓存工作线程](#缓存工作线程)收到[缓存系统](#缓存系统)正在更新缓存文件的通知，并且自上次清理以来已经过了这个时间间隔时，工作线程将尝试进行新的清理。

请同时参考 [`设置 allowed-clock-drift-for-files-from-future`](#设置-allowed-clock-drift-for-files-from-future)

## 设置 optimizing-compression-task-timeout

- **类型**：string(duration)
- **格式**：`"{integer}(s | m | h | d )"`
- **默认**：`"30m"`

当[缓存工作线程](#缓存工作线程)决定重新压缩缓存文件时，它确保在上一个 [`optimizing-compression-task-timeout`](#设置-optimizing-compression-task-timeout) 间隔内没有其他工作线程启动该文件的任务。如果一些工作线程已经开始工作，其他工作线程就会跳过这个任务。

请同时参考 [`设置 allowed-clock-drift-for-files-from-future`](#设置-allowed-clock-drift-for-files-from-future)

## 设置 allowed-clock-drift-for-files-from-future

- **类型**：string(duration)
- **格式**：`"{integer}(s | m | h | d )"`
- **默认**：`"1d"`

### Locks

当[缓存工作线程](#缓存工作线程)线程试图为某个任务获取一个锁时，它会检查其他工作线程是否已经获取了这样一个锁。为了容错并最终执行每个任务，锁会在一段时间后过期。然而，由于时钟漂移和不同的时区，将来可能会创建一些锁。此设置定义了这些锁的公差限制。如果系统中的时间已经改变(即两年后) ，[缓存系统](#缓存系统)应该仍然能够正常工作。因此，这些锁将被视为过期(假设公差不太大)。

### 缓存文件

与锁类似，缓存文件或其元数据可能在遥远的将来有修改时间。缓存系统试图尽可能长地保存这些文件。如果未达到限制，则不会删除缓存文件。否则，它们将被视为最古老的文件，因此它们可能会幸存下来。如果用户实际使用缓存文件，则修改时间将更新。

## 设置 file-count-soft-limit

- **类型**：string(SI prefix)
- **格式**：`"{integer}(K | M | G | T | P)?"`
- **默认**：`"65536"`

缓存目录中文件计数的软限制。

这不包括带有元数据的文件。要了解更多信息，请参考[缓存系统](#缓存系统)部分。

## 设置 files-total-size-soft-limit

- **类型**：string(disk space)
- **格式**：`"{integer}(K | Ki | M | Mi | G | Gi | T | Ti | P | Pi)?"`
- **默认**：`"512Mi"`

缓存目录中文件的总大小的软限制。

这不包括带有元数据的文件。要了解更多信息，请参考[缓存系统](#缓存系统)部分。

这是文件大小，而不是磁盘上物理占用的空间。

## 设置 file-count-limit-percent-if-deleting

- **类型**：string(percent)
- **格式**：`"{integer}%"`
- **默认**：`"70%"`

如果超过 [`file-count-soft-limit`](#设置-file-count-soft-limit)，并且[缓存工作线程](#缓存工作线程)执行清除任务，那么工作线程将删除一些缓存文件，因此在任务之后，文件计数不应该超过 [`file-count-soft-limit`](#设置-file-count-soft-limit) * [`file-count-limit-percent-if-deleting`](#设置-file-count-limit-percent-if-deleting)。

这不包括带有元数据的文件。要了解更多信息，请参考[缓存系统](#缓存系统)部分。

## 设置 files-total-size-limit-percent-if-deleting

- **类型**：string(percent)
- **格式**：`"{integer}%"`
- **默认**：`"70%"`

如果超过了 [`files-total-size-soft-limit`](#设置-files-total-size-soft-limit)，并且 [缓存工作线程](#缓存是如何工作的) 执行了清除任务，那么工作线程将删除一些缓存文件，因此在任务之后，文件总大小不应该超过 [`files-total-size-soft-limit`](#设置-files-total-size-soft-limit) * [`files-total-size-limit-cent-If-delete`](#设置-files-total-size-limit-percent-if-deleting)。

这不包括带有元数据的文件。要了解更多信息，请参考[缓存系统](#缓存系统)部分。

# 缓存是如何工作的？

这是一个实现细节，将来可能会改变。这里提供的信息旨在帮助理解整体情况和配置缓存。

有两个主要组件-缓存系统和缓存工作线程。

## 缓存系统

处理 GET 和 UPDATE 缓存请求。

- **GET 请求** - 只是从磁盘加载缓存，如果它在那里。
- **UPDATE 请求** - 使用 [zstd](https://facebook.github.io/zstd/) 和 [`baseline-compression-level`](#设置-baseline-compression-level) 接收到的数据，然后将数据写入磁盘。

如果成功处理请求，它将使用队列通知缓存工作器此事件。[`worker-event-queue-size`](#设置-worker-event-queue-size) 大小有限。如果已满，它将删除新事件，直到缓存工作线程从队列中弹出某个事件。

## 缓存工作线程

缓存工作线程在一个优先级较低的线程中运行，并在循环中从队列中弹出事件，逐个处理这些事件。

### 根据 GET 请求

1. 读取缓存文件的统计文件，增加使用率计数器并将其写回磁盘。

2. 如果满足以下所有条件，尝试重新压缩缓存文件:

    - 使用率计数器超过 [`optimized-compression-usage-count-threshold`](#设置-optimized-compression-usage-counter-threshold),
    - 文件的压缩级别低于 [`optimizing-compression-task-timeout`](#设置-optimized-compression-level),
    - 在 [`optimizing-compression-task-timeout`](#设置-optimizing-compression-task-timeout) 间隔内，没有其他工作线程开始处理这个特定的任务。

    在重新压缩时，[`optimized-compression-level`](#设置-optimized-compression-level) 用作压缩级别。  

### 根据 UPDATE 请求

1. 为缓存文件编写一个新的统计文件。

2. 如果在上一次 [`cleanup-interval`](#设置-cleanup-interval) 内没有工作线程尝试清理缓存，则清理该缓存。在此任务期间:
    - 将删除缓存目录中所有无法识别的文件和过期的任务锁
    - 如果超过 [`file-count-soft-limit`](#设置-file-count-soft-limit) 或 [`files-total-size-soft-limit`](#设置-files-total-size-limit-percent-if-deleting)，则将根据 [`file-count-limit-percent-if-deleting`](#设置-file-count-limit-percent-if-deleting) 和 [`files-total-size-limit-percent-if-deleting`](#设置-files-total-size-limit-percent-if-deleting) 来删除已识别的文件。Wasmtime 使用 [最少最近使用(LRU)](https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_recently_used_(LRU)) 缓存替换策略，并要求文件系统维护文件的适当 mtime (修改时间)。具有未来 mtimes 的文件被特别处理-更多细节在 [`allowed-clock-drift-for-files-from-future`](#设置-allowed-clock-drift-for-files-from-future)。

### 元数据文件

- 每个缓存的 WebAssembly 模块都有自己的统计文件
- 每个锁都是一个文件
