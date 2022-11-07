# 交叉编译Python

首先确保安装了交叉编译工具链

Ubuntu 22.04

https://github.com/karthickai/python-arm-xcompile

TARGET_HOST="aarch64-himix200-linux"

sudo apt-get install build-essential

chmod +x compile_python.sh

./compile_python.sh

```
......
aarch64-himix200-linux-gcc -Wno-unused-result -Wsign-compare -DNDEBUG -g -fwrapv -O3 -Wall    -std=c99 -Wextra -Wno-unused-result -Wno-unused-parameter -Wno-missing-field-initializers   -I. -I./Include    -DPy_BUILD_CORE  -I/home/yxj/Desktop/python_xcompile/_install/include -c ./Modules/zlibmodule.c -o Modules/zlibmodule.o
./Modules/zlibmodule.c:10:10: fatal error: zlib.h: No such file or directory
 #include "zlib.h"
          ^~~~~~~~
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

解决尝试：`sudo apt-get install libz-dev`

```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'zlib1g-dev' instead of 'libz-dev'
The following NEW packages will be installed:
  zlib1g-dev
......
```

`rm -rf python_xcompile` 之后再来一次

还是一样的错误。

删除脚本中`ENABLE_MODULES`的zlib再试一遍……

`rm -rf python_xcompile && ./compile_python.sh`

(https://zlib.net 实在不行就自己下载源码的头文件和库文件了  下方有下载按钮  解压出来是有一个97KB的zlib.h)

但是报了别的错：

```
_PYTHON_PROJECT_BASE=/home/yxj/Desktop/python_xcompile/Python-3.6.8 _PYTHON_HOST_PLATFORM=linux-aarch64 PYTHONPATH=./Lib _PYTHON_SYSCONFIGDATA_NAME=_sysconfigdata_m_linux_aarch64-linux-gnu python -S -m sysconfig --generate-posix-vars ;\
if test $? -ne 0 ; then \
	echo "generate-posix-vars failed" ; \
	rm -f ./pybuilddir.txt ; \
	exit 1 ; \
fi
/bin/sh: 1: python: not found
generate-posix-vars failed
make: *** [Makefile:581: pybuilddir.txt] Error 1
```

https://stackoverflow.com/questions/56178633/trouble-cross-compiling-python

所以我先在Ubuntu 22.04上装一个miniconda：https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html，选最新的装 `Python 3.9 Miniconda3 Linux 64-bit` ，之后新建一个环境用3.6.8就行了

conda create -n crosspy221107 python=3.6.8
conda activate crosspy221107
python -V
Python 3.6.8 :: Anaconda, Inc.

`rm -rf python_xcompile && ./compile_python.sh`

  File "/home/yxj/Desktop/python_xcompile/Python-3.6.8/Lib/platform.py", line 1225, in _sys_version
    repr(sys_version))
ValueError: failed to parse CPython sys.version: '3.6.8 |Anaconda, Inc.| (default, Dec 30 2018, 01:22:34) \n[GCC 7.3.0]'
make: *** [Makefile:1102: install] Error 1

https://stackoverflow.com/questions/34145861/valueerror-failed-to-parse-cpython-sys-version-after-using-conda-command

python=/home/yxj/miniconda3/envs/crosspy221107/bin/python
export python=/home/yxj/miniconda3/envs/crosspy221107/bin/python

`rm -rf python_xcompile && ./compile_python.sh`

还是一样的报错……删了conda吧，还是得用正版python

emmm 没有那么麻烦哈  直接 `which python3; sudo apt-get python-is-python3`

```
configure: WARNING: unrecognized options: --enable-unicode
checking build system type... x86_64-pc-linux-gnu
checking host system type... aarch64-himix200-linux-gnu
checking for python3.6... no
checking for python3... python3
checking for python interpreter for cross build... configure: error: python3.6 interpreter not found
make: *** No targets specified and no makefile found.  Stop.
make: *** No rule to make target 'install'.  Stop.
```

https://askubuntu.com/a/865644

```
$ pyenv install 3.6.8
Downloading Python-3.6.8.tar.xz...
-> https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tar.xz
Installing Python-3.6.8...

BUILD FAILED (Ubuntu 22.04 using python-build 20180424)

Inspect or clean up the working tree at /tmp/python-build.20221107100131.85759
Results logged to /tmp/python-build.20221107100131.85759.log

Last 10 log lines:
if test "xupgrade" != "xno"  ; then \
	case upgrade in \
		upgrade) ensurepip="--upgrade" ;; \
		install|*) ensurepip="" ;; \
	esac; \
	 ./python -E -m ensurepip \
		$ensurepip --root=/ ; \
fi
Segmentation fault (core dumped)
make: *** [Makefile:1102: install] Error 139
# 估计是内存不够了  我从2G拉到4G  给了4G内存还是不行  太菜了
```

https://www.python.org/downloads/release/python-368/

还是自己装一个吧……笑了


wget https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tar.xz
tar xvf Python-3.6.8.tar.xz
cd Python-3.6.8/
./configure
sudo make altinstall

```
/usr/bin/install -c -m 644 ./Misc/python.man \
	/usr/local/share/man/man1/python3.6.1
if test "xupgrade" != "xno"  ; then \
	case upgrade in \
		upgrade) ensurepip="--altinstall --upgrade" ;; \
		install|*) ensurepip="--altinstall" ;; \
	esac; \
	 ./python -E -m ensurepip \
		$ensurepip --root=/ ; \
fi
Segmentation fault (core dumped)
make: *** [Makefile:1112: altinstall] Error 139

```

```
which python3.6
/usr/local/bin/python3.6  
```

已经装上了 不管了

`rm -rf python_xcompile && ./compile_python.sh`

不是内存的问题，绷不住了
