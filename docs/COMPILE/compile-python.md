# 交叉编译 Python3.10.8

> 此文构建的 Python 含 pip/zlib/ffi

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

## 将zlib加入Python的编译脚本

`compile-python.sh`:

```sh
#!/bin/bash
TARGET_HOST="aarch64-himix200-linux" # 这一行有修改 换成HiLinux的编译工具链
ROOT_FILESYSTEM="/usr/arm-linux-gnueabi/"
BUILD_HOST="x86_64-linux-gnu" # find out with uname -m
PYTHON_VERSION="3.10.8"
PYTHON_ARCHIVE="Python-$PYTHON_VERSION.tar.xz"
ROOT_DIR="$HOME/Desktop/HiLinux"
SOURCE_DIR="Python-$PYTHON_VERSION"
INSTALL_DIR="${ROOT_DIR}/libraries"

export RFS="$ROOT_FILESYSTEM"
mkdir -p $ROOT_DIR $INSTALL_DIR

ENABLE_MODULES="_ctypes zlib array cmath binascii _collections cPickle cStringIO datetime
_elementtree fcntl _functools itertools _io math _md5 operator _random select
_sha _socket _struct termios time unicodedata"

# Step 1 - Downloading Python and extracting
cd $ROOT_DIR
wget -c http://www.python.org/ftp/python/$PYTHON_VERSION/$PYTHON_ARCHIVE
tar -xf $PYTHON_ARCHIVE
cd $SOURCE_DIR

# Step 2 - Enable modules by uncommenting them in Modules/Setup.dist
for module in $ENABLE_MODULES
do
    sed "s/^#$module/$module/" -i Modules/Setup # is `-i Modules/Setup.dist` before in Python3.6
done

# Step 3 - Cross-Compile
make distclean
# 在这里添加刚才编译好的zlib
CFLAGS="-I$INSTALL_DIR/include" CPPFLAGS="-I$INSTALL_DIR/include" LDFLAGS="-L$INSTALL_DIR/lib -L$INSTALL_DIR/lib64" ./configure --host=$TARGET_HOST --build=$BUILD_HOST --prefix=$INSTALL_DIR --disable-ipv6 ac_cv_file__dev_ptmx=no ac_cv_file__dev_ptc=no ac_cv_have_long_long_format=yes --with-ensurepip=install
make -j4 # 可以加-j多核编译来加速
make install

cd $ROOT_DIR
rm -rf $SOURCE_DIR $PYTHON_ARCHIVE
```

```sh
$ vim compile-python.sh
$ chmod +x compile-python.sh
$ ./compile-python.sh
```

执行完这个脚本可以在 `INSTALL_DIR/bin` 中找到Python。

## 安装

按照 [安装 libraries](./install-libs.md) 中的步骤进行安装。

## 查看效果

打开 HiLinux：

```sh
$ which python3
/root/yxj/SOFTWARE/libraries/bin/python3
$ python3 -V
Python 3.10.8
$ python3
Python 3.10.8 (main, Nov 19 2022, 18:09:06) [GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import zlib
>>> import ctypes
>>> import time
>>> time.time()
9486.930260722
>>>

$ python3 -m ensurepip --default-pip # 初始化pip
Installing collected packages: setuptools, pip
Successfully installed pip-22.2.2 setuptools-63.2.0
$ which pip3
/root/yxj/SOFTWARE/libraries/bin/pip3
$ pip3 -V
pip 22.2.2 from /root/yxj/SOFTWARE/libraries/lib/python3.10/site-packages/pip (python 3.10)
```

## 参考资料

- [j-c-cook/cross-compile-python.md](https://gist.github.com/j-c-cook/2a291dc0bfaa2f6639272e344ff66e62)
- [building Python from source with zlib support](https://stackoverflow.com/a/15013895/14298786)
