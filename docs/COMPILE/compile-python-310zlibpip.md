# 交叉编译Python3.10.8 含pip和zlib

请使用 `Ubuntu 22.04`，这个版本测试成功编译。

系统自带与需要编译的版本相同的 `Python3.10`，这省去了需要在本机安装 `Python` 的过程：

```sh
$ python3 -V
Python 3.10.6
```

首先确保安装了 `aarch64-himix200-linux` 交叉编译工具链。

其次安装 `make`：

```sh
$ sudo apt-get install build-essential
```

## 手动编译zlib

`compile-zlib.sh`:

```sh
#!/bin/bash

wget https://zlib.net/zlib1213.zip
unzip zlib1213.zip
cd zlib-1.2.13/
export cross=aarch64-himix200-linux
CHOST="${cross}" CC="${cross}-gcc" CXX="${cross}-g++" AR="${cross}-ar" LD="${cross}-ld" RANLIB="${cross}-ranlib" ./configure --prefix=$HOME/Desktop/HiLinux-zlib
make -j4
make install
cd ..
```

```sh
$ chmod +x compile-zlib.sh
$ ./compile-zlib.sh
```

这样在桌面上就会出现一个已经安装好的zlib，在文件夹 `HiLinux-zlib` 中，内容为：

```
$ tree
.
├── include
│   ├── zconf.h
│   └── zlib.h
├── lib
│   ├── libz.a
│   ├── libz.so -> libz.so.1.2.13
│   ├── libz.so.1 -> libz.so.1.2.13
│   ├── libz.so.1.2.13
│   └── pkgconfig
│       └── zlib.pc
└── share
    └── man
        └── man3
            └── zlib.3
```

## 将zlib加入Python的编译脚本

`compile-python.sh`:

```sh
#!/bin/bash

TARGET_HOST="aarch64-himix200-linux" # 这一行有修改 换成HiLinux的编译工具链
ROOT_FILESYSTEM="/usr/arm-linux-gnueabi/"
BUILD_HOST="x86_64-linux-gnu" # find out with uname -m
WORKING_DIRECTORY="temp-python-compile"
PYTHON_VERSION="3.10.8"
INSTALL_DIRECTORY="$HOME/Desktop/HiLinux-Python$PYTHON_VERSION"
SOURCE_DIRECTORY="Python-$PYTHON_VERSION"
PYTHON_ARCHIVE="Python-$PYTHON_VERSION.tar.xz"
ENABLE_MODULES="array cmath binascii _collections cPickle cStringIO datetime
_elementtree fcntl _functools itertools _io math _md5 operator _random select
_sha _socket _struct termios time unicodedata zlib"

# Preparing compile environment
export RFS="$ROOT_FILESYSTEM"
mkdir -p "$WORKING_DIRECTORY"
mkdir -p "$INSTALL_DIRECTORY"
PREFIX=$(readlink --no-newline --canonicalize "$INSTALL_DIRECTORY")

# Step 1 - Downloading Python and extracting
cd $WORKING_DIRECTORY
wget -c http://www.python.org/ftp/python/$PYTHON_VERSION/$PYTHON_ARCHIVE
rm -rf $SOURCE_DIRECTORY
tar -xf $PYTHON_ARCHIVE
cd $SOURCE_DIRECTORY

# Step 2 - Enable modules by uncommenting them in Modules/Setup.dist
for module in $ENABLE_MODULES
do
    sed "s/^#$module/$module/" -i Modules/Setup # is `-i Modules/Setup.dist` before in Python3.6
done

# Step 3 - Cross-Compile
make distclean
# 在这里添加刚才编译好的zlib
CFLAGS="-I$HOME/Desktop/HiLinux-zlib/include" CPPFLAGS="-I$HOME/Desktop/HiLinux-zlib/include" LDFLAGS="-L$HOME/Desktop/HiLinux-zlib/lib" ./configure --host=$TARGET_HOST --build=$BUILD_HOST --prefix=$PREFIX --disable-ipv6 ac_cv_file__dev_ptmx=no ac_cv_file__dev_ptc=no ac_cv_have_long_long_format=yes --with-ensurepip=install
make -j4 # 可以加-j多核编译来加速
make install
```

```sh
$ chmod +x compile-python.sh
$ ./compile-python.sh
```

执行完这个脚本可以在 `INSTALL_DIRECTORY` 中找到Python。

## 打包Python和zlib库文件

```sh
$ cd $HOME/Desktop/
$ tar -cf HiLinux-Python3.10.8.tar HiLinux-Python3.10.8
$ tar -cf HiLinux-zlib.tar HiLinux-zlib
```

将 HiLinux-Python3.10.8.tar 和 HiLinux-zlib.tar 用 ftp 传到 HiLinux 上并解压：

```sh
$ tar -xf HiLinux-Python3.10.8.tar
$ tar -xf HiLinux-zlib.tar
```

## 设置环境变量

在 `~/.profile` 中添加：

```
export PATH="$PATH:/root/yxj/SOFTWARE/HiLinux-Python3.10.8/bin/"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/root/yxj/SOFTWARE/HiLinux-zlib/lib"
```

```sh
$ source ~/.profile
```

## 查看效果

```sh
$ python -V
Python 3.10.8
$ python -m ensurepip --default-pip
$ pip -V
pip 22.2.2 from /root/yxj/SOFTWARE/HiLinux-Python3.10.8/lib/python3.10/site-packages/pip (python 3.10)
```

## 参考资料

- [j-c-cook/cross-compile-python.md](https://gist.github.com/j-c-cook/2a291dc0bfaa2f6639272e344ff66e62)
- [building Python from source with zlib support](https://stackoverflow.com/a/15013895/14298786)
