# 编译带有zlib的Python3.10.8

> 直接下载链接 [HiLinux-Python3.10.8.tar](https://cloud.tsinghua.edu.cn/f/f41abcedf7f447f28bea/)

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
CFLAGS="-I$HOME/Desktop/HiLinux-zlib/include" LDFLAGS="-L$HOME/Desktop/HiLinux-zlib/lib" CPPFLAGS="-I$HOME/Desktop/HiLinux-zlib/include" ./configure --host=$TARGET_HOST --build=$BUILD_HOST --prefix=$PREFIX --disable-ipv6 ac_cv_file__dev_ptmx=no ac_cv_file__dev_ptc=no ac_cv_have_long_long_format=yes
make -j4 # 可以加-j多核编译来加速
make install
```

```sh
$ chmod +x compile-python.sh
$ ./compile-python.sh
```

执行完这个脚本可以在 `INSTALL_DIRECTORY` 中找到Python。

### 命令行输出结果

```
compilation terminated.

The necessary bits to build these optional modules were not found:
_bz2                  _curses               _curses_panel      
_dbm                  _gdbm                 _hashlib           
_lzma                 _sqlite3              _ssl               
_tkinter              _uuid                 nis                
readline                                                       
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  pwd                   time               


Failed to build these modules:
_ctypes                                                        


Could not build the ssl module!
Python requires a OpenSSL 1.1.1 or newer
```

```
running build_scripts
copying and adjusting /home/yxj/Desktop/temp-python-compile/Python-3.10.8/Tools/scripts/pydoc3 -> build/scripts-3.10
copying and adjusting /home/yxj/Desktop/temp-python-compile/Python-3.10.8/Tools/scripts/idle3 -> build/scripts-3.10
copying and adjusting /home/yxj/Desktop/temp-python-compile/Python-3.10.8/Tools/scripts/2to3 -> build/scripts-3.10
changing mode of build/scripts-3.10/pydoc3 from 664 to 775
changing mode of build/scripts-3.10/idle3 from 664 to 775
changing mode of build/scripts-3.10/2to3 from 664 to 775
renaming build/scripts-3.10/pydoc3 to build/scripts-3.10/pydoc3.10
renaming build/scripts-3.10/idle3 to build/scripts-3.10/idle3.10
renaming build/scripts-3.10/2to3 to build/scripts-3.10/2to3-3.10
……
Processing /tmp/tmp09j8evds/setuptools-63.2.0-py3-none-any.whl
Processing /tmp/tmp09j8evds/pip-22.2.2-py3-none-any.whl
Installing collected packages: setuptools, pip
  WARNING: The scripts pip3 and pip3.10 are installed in '/home/yxj/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
Successfully installed pip-22.2.2 setuptools-63.2.0
```

### 报错

在最开始的时候，我们写的 `compile-python.sh` 的脚本报错：

```
sed: can't read Modules/Setup.dist: No such file or directory
```

将 `Modules/Setup.dist` 改为 `Modules/Setup`，重新编译：

```
compilation terminated.

The necessary bits to build these optional modules were not found:
_bz2                  _curses               _curses_panel      
_dbm                  _gdbm                 _hashlib           
_lzma                 _sqlite3              _ssl               
_tkinter              _uuid                 nis                
readline                                                       
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  _elementtree          _md5               
_random               _sha1                 _sha256            
_sha3                 _sha512               _socket            
_struct               array                 binascii           
cmath                 fcntl                 math               
pwd                   select                termios            
time                  unicodedata           zlib               


Failed to build these modules:
_ctypes                                                        


Could not build the ssl module!
Python requires a OpenSSL 1.1.1 or newer
```

好像没什么区别……因为好像默认都构建了……甚至zlib可能也支持了，不知道手动编译zlib还有没有什么用。

两种版本的安装包，都可以正常 import collections io itertools math operator re struct sys warnings zlib 这些包，问题不大，只能说Python3.6可能有些菜。

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

### 设置环境变量

在 `~/.profile` 中添加：

```
alias python3=/root/yxj/SOFTWARE/HiLinux-Python3.10.8/bin/python3.10
alias python=/root/yxj/SOFTWARE/HiLinux-Python3.10.8/bin/python3.10
```

执行 `python -V` 发现报错：

```
$ python -V
/root/yxj/SOFTWARE/HiLinux-Python3.10.8/bin/python3.10: error while loading shared libraries: libz.so.1: cannot open shared object file: No such file or directory
```

在 `~/.profile` 中添加：

```
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/root/yxj/SOFTWARE/HiLinux-zlib/lib"
```

这次没问题了，`import` 也是可以用的：

```
$ python -V
Python 3.10.8
$ python
Python 3.10.8 (default, Nov 14 2022, 14:45:02)
[GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import zlib
>>>
```

> 备注：也可以在 /etc/profile 内直接改环境变量。

## 参考资料

- [j-c-cook/cross-compile-python.md](https://gist.github.com/j-c-cook/2a291dc0bfaa2f6639272e344ff66e62)
- [building Python from source with zlib support](https://stackoverflow.com/a/15013895/14298786)
