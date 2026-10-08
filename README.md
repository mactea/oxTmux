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
./tmux -V
```

## 测试

```sh
cd regress
make                 # 全部回归测试（耗时较长）
make buffers.sh      # 单个测试
```

## 与上游同步

```sh
git fetch upstream
git merge upstream/master
```

## 许可证

- 来自 tmux 上游的代码使用 ISC 许可证，见各源文件头部和 [`COPYING`](COPYING)。
- oxTmux 新增的代码使用 MIT 许可证，见 [`LICENSE`](LICENSE)。
