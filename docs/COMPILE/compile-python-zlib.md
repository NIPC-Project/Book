# 编译带有zlib的Python3.6.15

> 直接使用链接 [HiLinux-Python3.6.15.tar](https://cloud.tsinghua.edu.cn/f/9b269478ff794cef9aa1/)

由于实验中需要使用 pypng 这个库，这个库依赖 `zlib`，所以需要在 Python 的模块中加入 zlib

加入后重新编译的结果是：

```
compilation terminated.
make: *** [Makefile:1843: Modules/zlibmodule.o] Error 1
Makefile:1810: warning: overriding recipe for target 'Modules/_math.o'
Makefile:590: warning: ignoring old recipe for target 'Modules/_math.o'
Makefile:1813: warning: overriding recipe for target 'Modules/_math.o'
Makefile:1810: warning: ignoring old recipe for target 'Modules/_math.o'
aarch64-himix200-linux-gcc -Wno-unused-result -Wsign-compare -DNDEBUG -g -fwrapv -O3 -Wall    -std=c99 -Wextra -Wno-unused-result -Wno-unused-parameter -Wno-missing-field-initializers   -I. -I./Include    -DPy_BUILD_CORE  -I/home/yxj/Desktop/python_xcompile/_install/include -c ./Modules/zlibmodule.c -o Modules/zlibmodule.o
./Modules/zlibmodule.c:10:10: fatal error: zlib.h: No such file or directory
 #include "zlib.h"
          ^~~~~~~~
compilation terminated.
make: *** [Makefile:1843: Modules/zlibmodule.o] Error 1
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
PYTHON_VERSION="3.6.15"
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
    sed "s/^#$module/$module/" -i Modules/Setup.dist
done

# Step 3 - Cross-Compile
make distclean
# 在这里添加刚才编译好的zlib
CFLAGS="-I$HOME/Desktop/HiLinux-zlib/include" LDFLAGS="-L$HOME/Desktop/HiLinux-zlib/lib" CPPFLAGS="-I$HOME/Desktop/HiLinux-zlib/include" ./configure --host=$TARGET_HOST --build=$BUILD_HOST --prefix=$PREFIX --disable-ipv6 ac_cv_file__dev_ptmx=no ac_cv_file__dev_ptc=no ac_cv_have_long_long_format=yes
make -j4 # 可以加-j多核编译来加速
make install
```

执行完这个脚本可以在 `INSTALL_DIRECTORY` 中找到Python。

### 备注 Segmentation fault (core dumped)

虽然出现了下面的问题，但是看起来已经成功编译（make）了，是 `make install` 步骤出了一些问题……

```
running install_scripts
copying build/scripts-3.6/pyvenv-3.6 -> /home/yxj/Desktop/HiLinux-Python3.6.15/bin
copying build/scripts-3.6/idle3.6 -> /home/yxj/Desktop/HiLinux-Python3.6.15/bin
copying build/scripts-3.6/2to3-3.6 -> /home/yxj/Desktop/HiLinux-Python3.6.15/bin
copying build/scripts-3.6/pydoc3.6 -> /home/yxj/Desktop/HiLinux-Python3.6.15/bin
changing mode of /home/yxj/Desktop/HiLinux-Python3.6.15/bin/pyvenv-3.6 to 775
changing mode of /home/yxj/Desktop/HiLinux-Python3.6.15/bin/idle3.6 to 775
changing mode of /home/yxj/Desktop/HiLinux-Python3.6.15/bin/2to3-3.6 to 775
changing mode of /home/yxj/Desktop/HiLinux-Python3.6.15/bin/pydoc3.6 to 775
rm /home/yxj/Desktop/HiLinux-Python3.6.15/lib/python3.6/lib-dynload/_sysconfigdata_m_linux_aarch64-linux-gnu.py
rm -r /home/yxj/Desktop/HiLinux-Python3.6.15/lib/python3.6/lib-dynload/__pycache__
Creating directory /home/yxj/Desktop/HiLinux-Python3.6.15/share/man
Creating directory /home/yxj/Desktop/HiLinux-Python3.6.15/share/man/man1
/usr/bin/install -c -m 644 ./Misc/python.man \
	/home/yxj/Desktop/HiLinux-Python3.6.15/share/man/man1/python3.6.1
if test ! -d /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig; then \
	echo "Creating directory /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig"; \
	/usr/bin/install -c -d -m 755 /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig; \
fi
if test -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/python3 -o -h /home/yxj/Desktop/HiLinux-Python3.6.15/bin/python3; \
then rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/python3; \
else true; \
fi
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s python3.6 python3)
if test "3.6" != "3.6m"; then \
	rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/python3.6-config; \
	(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s python3.6m-config python3.6-config); \
	rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig/python-3.6m.pc; \
	(cd /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig; ln -s python-3.6.pc python-3.6m.pc); \
fi
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/python3-config
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s python3.6-config python3-config)
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig/python3.pc
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/lib/pkgconfig; ln -s python-3.6.pc python3.pc)
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/idle3
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s idle3.6 idle3)
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/pydoc3
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s pydoc3.6 pydoc3)
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/2to3
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s 2to3-3.6 2to3)
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/pyvenv
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s pyvenv-3.6 pyvenv)
if test "x" != "x" ; then \
	rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/bin/python3-32; \
	(cd /home/yxj/Desktop/HiLinux-Python3.6.15/bin; ln -s python3.6-32 python3-32) \
fi
rm -f /home/yxj/Desktop/HiLinux-Python3.6.15/share/man/man1/python3.1
(cd /home/yxj/Desktop/HiLinux-Python3.6.15/share/man/man1; ln -s python3.6.1 python3.1)
if test "xupgrade" != "xno"  ; then \
	case upgrade in \
		upgrade) ensurepip="--upgrade" ;; \
		install|*) ensurepip="" ;; \
	esac; \
	 _PYTHON_PROJECT_BASE=/home/yxj/Desktop/temp-python-compile/Python-3.6.15 _PYTHON_HOST_PLATFORM=linux-aarch64 PYTHONPATH=/home/yxj/Desktop/temp-python-compile/Python-3.6.15/build/lib.linux-aarch64-3.6:./Lib _PYTHON_SYSCONFIGDATA_NAME=_sysconfigdata_m_linux_aarch64-linux-gnu python3.6 -m ensurepip \
		$ensurepip --root=/ ; \
fi
Segmentation fault (core dumped)
make: *** [Makefile:1102: install] Error 139
```

## 打包Python

```sh
$ tar -cf HiLinux-Python3.6.15.tar $HOME/Desktop/HiLinux-Python3.6.15
```

将 HiLinux-Python3.6.15.tar 用 ftp 传到 HiLinux 上。

```sh
$ tar -xf HiLinux-Python3.6.15.tar
$ cd python3.6/bin
$ ls
2to3               pydoc3             python3.6          pyvenv
2to3-3.6           pydoc3.6           python3.6-config   pyvenv-3.6
idle3              python3            python3.6m
idle3.6            python3-config     python3.6m-config
```

## 参考资料

- [j-c-cook/cross-compile-python.md](https://gist.github.com/j-c-cook/2a291dc0bfaa2f6639272e344ff66e62)
- [building Python from source with zlib support](https://stackoverflow.com/a/15013895/14298786)
