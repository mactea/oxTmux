# oxTmux 项目指引

oxTmux 是 tmux 的增强版本（fork）。本仓库带有 tmux 的完整上游历史，在上游代码之上加功能。

## 目录与仓库

| 路径 | 作用 |
|---|---|
| `/home/cm/cmcode/oxTmux` | 本项目主目录，所有开发都在这里做 |
| `/home/cm/cmcode/gitTmux` | tmux 上游的原始克隆，**只作参考，不要修改** |

| remote | 地址 | 用途 |
|---|---|---|
| `origin` | `https://github.com/mactea/oxTmux.git` | 本项目 |
| `upstream` | `https://github.com/tmux/tmux.git` | tmux 上游，只拉取，不推送 |

- 主分支是 `main`。功能在 `feature/<名字>` 分支上开发，再合回 `main`。
- 同步上游：`git fetch upstream && git merge upstream/master`。用 merge，不用 rebase，避免改写已推送的历史。

## 构建与测试

```sh
sh autogen.sh && ./configure && make   # 首次，或改了 configure.ac / Makefile.am 之后
make                                   # 平时增量构建
./oxtmux -V
cd regress && make buffers.sh          # 跑单个回归测试
cd regress && make                     # 跑全部回归测试，耗时长
```

- 构建产物（`*.o`、`oxtmux`、`Makefile`、`configure` 等）已被 `.gitignore` 忽略。
- 测试时用独立 socket（`./oxtmux -L oxtest ...`），跑完后 `./oxtmux -L oxtest kill-server`。
- 单独跑测试脚本要带 `TEST_TMUX=$(readlink -f ../oxtmux)`；脚本默认找 `../tmux`，`regress/Makefile` 已经替你传了。
- `regress/alerts.sh` 单独跑超过 60 秒，写超时时注意。

## 改名：程序叫 oxtmux

已改动的地方（合并上游冲突时注意保留）：

- `Makefile.am`：`bin_PROGRAMS = oxtmux`，所有 `tmux_SOURCES` / `tmux_OBJECTS` 变量改成 `oxtmux_` 前缀；man 页安装成 `oxtmux.1`。
  上游往 `dist_tmux_SOURCES` 加文件时，要加到 `dist_oxtmux_SOURCES`。
- `tmux.c`：socket 目录是 `oxtmux-<UID>`，`-V` 输出 `oxtmux <版本>`。
- `regress/Makefile`：传 `TEST_TMUX` 指向 `../oxtmux`。
- 没改：配置文件路径（仍读 `~/.tmux.conf`）、`$TMUX` 环境变量、`configure.ac` 的包名、源文件名。

## 修改原则

- **尽量少改上游文件**。改动越分散，合并上游时冲突越多。
  - 新功能优先放进新文件（如 `ox-<功能>.c`），上游文件里只加最少的挂接代码。
  - 在上游文件里改动时，加 `/* oxTmux: ... */` 注释，方便合并冲突时识别。
- 新增 `.c` 文件要加进 `Makefile.am` 的 `dist_oxtmux_SOURCES`，然后重新跑 `sh autogen.sh && ./configure`。
- 不要改 `.github/workflows/` 下的上游 workflow；它们有 `github.repository == 'tmux/tmux'` 条件，在本仓库不会运行。

## tmux 代码约定

照上游风格写（OpenBSD KNF）：

- 缩进用 Tab，续行缩进 4 个空格；每行不超过 80 列。
- C99（`-std=gnu99`），变量声明放在块开头（编译带 `-Wdeclaration-after-statement`）。
- 内存分配用 `xmalloc` / `xcalloc` / `xstrdup` / `xasprintf`，不直接调 `malloc`。
- 新文件头部带许可证注释，格式参照现有源文件。

常见扩展点：

| 要加的东西 | 改哪里 |
|---|---|
| 新命令 | 新建 `cmd-<名字>.c` 定义 `cmd_<名字>_entry`；在 `cmd.c` 加 `extern` 声明并加进 `cmd_table`；加进 `Makefile.am`；在 `tmux.1` 写文档 |
| 新选项 | `options-table.c`；在 `tmux.1` 写文档 |
| 新格式变量 | `format.c` |
| 默认按键 | `key-bindings.c` |

## 提交

- 本仓库的提交身份已在 local 配置：`cm <mactes@gmail.com>`。
- 提交信息结尾按会话里给的署名行写。
