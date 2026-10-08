# oxTmux

oxTmux 是 [tmux](https://github.com/tmux/tmux) 的增强版本。代码基于 tmux 上游 `master`，
保留完整的上游提交历史，并定期合并上游更新。

上游 tmux 的原始说明见 [`README`](README)，变更记录见 [`CHANGES`](CHANGES)。

## 构建

依赖（Ubuntu / Debian）：

```sh
sudo apt-get install autoconf automake bison pkg-config libevent-dev libncurses-dev
```

从源码构建：

```sh
sh autogen.sh
./configure
make
./oxtmux -V
```

程序名是 `oxtmux`，`make install` 默认装到 `/usr/local/bin/oxtmux`，man 页是 `oxtmux.1`。
它和系统自带的 tmux 互不干扰：socket 目录是 `/tmp/oxtmux-<UID>`，不和 tmux 的 `/tmp/tmux-<UID>` 共用。
配置文件和 tmux 相同（`~/.tmux.conf` 等）。

## 测试

```sh
cd regress
make                 # 全部回归测试（耗时较长）
make buffers.sh      # 单个测试
```

单独执行某个测试脚本时，要指定程序路径：`TEST_TMUX=$(readlink -f ../oxtmux) sh buffers.sh`。

## 与上游同步

```sh
git fetch upstream
git merge upstream/master
```

## 许可证

- 来自 tmux 上游的代码使用 ISC 许可证，见各源文件头部和 [`COPYING`](COPYING)。
- oxTmux 新增的代码使用 MIT 许可证，见 [`LICENSE`](LICENSE)。
