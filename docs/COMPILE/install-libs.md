# HiLinux 安装 libraries

将 Ubuntu 上得到的 `libraries` 文件夹打包：

```sh
tar -cf libraries.tar libraries/
```

其中内容为：

```
.
├── bin
│   ├── 2to3 -> 2to3-3.10
│   ├── 2to3-3.10
│   ├── idle3 -> idle3.10
│   ├── idle3.10
│   ├── libpng-config -> libpng16-config
│   ├── libpng16-config
│   ├── png-fix-itxt
│   ├── pngfix
│   ├── pydoc3 -> pydoc3.10
│   ├── pydoc3.10
│   ├── python3 -> python3.10
│   ├── python3-config -> python3.10-config
│   ├── python3.10
│   └── python3.10-config
├── include
│   ├── ffi.h
│   ├── ffitarget.h
│   ├── libpng16
│   ├── png.h -> libpng16/png.h
│   ├── pngconf.h -> libpng16/pngconf.h
│   ├── pnglibconf.h -> libpng16/pnglibconf.h
│   ├── python3.10
│   ├── zconf.h
│   └── zlib.h
├── lib
│   ├── libpng.a -> libpng16.a
│   ├── libpng.la -> libpng16.la
│   ├── libpng.so -> libpng16.so
│   ├── libpng16.a
│   ├── libpng16.la
│   ├── libpng16.so -> libpng16.so.16.39.0
│   ├── libpng16.so.16 -> libpng16.so.16.39.0
│   ├── libpng16.so.16.39.0
│   ├── libpython3.10.a
│   ├── libz.a
│   ├── libz.so -> libz.so.1.2.13
│   ├── libz.so.1 -> libz.so.1.2.13
│   ├── libz.so.1.2.13
│   ├── pkgconfig
│   └── python3.10
├── lib64
│   ├── libffi.a
│   ├── libffi.la
│   ├── libffi.so -> libffi.so.8.1.2
│   ├── libffi.so.8 -> libffi.so.8.1.2
│   └── libffi.so.8.1.2
└── share
    ├── info
    └── man
```

对于我们来说，主要包含 Python 3.10 和 libpng。Python 可以极大提高编程调试效率，libpng 则需要在项目中用来编码 PNG 文件。

> 你可以不通过 Ubuntu 构建，直接下载已经构建打包好的 `libraries.tar`：
> 
> <https://cloud.tsinghua.edu.cn/f/66cf1871b6d347e598b8/>

用 FTP 将 `libraries.tar` 传到 HiLinux， 在 HiLinux 上解压得到 `libraries`，路径记为 `INSTALL_DIR`。

```sh
tar -xf libraries.tar
```

在 `~/.profile` 中添加：

```sh
INSTALL_DIR=/root/yxj/SOFTWARE/libraries # modify it to your path
export PATH="$PATH:$INSTALL_DIR/bin/"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$INSTALL_DIR/lib:$INSTALL_DIR/lib64"
```

```sh
$ source ~/.profile
```
