# 设置HiLinux环境变量

将 Ubuntu 上得到的 `libraries` 文件夹打包：

```sh
tar -cf libraries.tar libraries/
```

用 ftp 将 `libraries.tar` 传到 HiLinux， 在 HiLinux 上解压得到 `libraries`，路径记为 `INSTALL_DIR`。

```sh
tar -xf libraries/
```

在 `~/.profile` 中添加：

```sh
INSTALL_DIR=path/to/libraries # modify it to your path
export PATH="$PATH:$INSTALL_DIR/bin/"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$INSTALL_DIR/lib:$INSTALL_DIR/lib64"
```

```sh
$ source ~/.profile
```
