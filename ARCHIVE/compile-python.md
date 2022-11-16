# 使用HiLinux编译工具链交叉编译Python3.6.8

> 已归档。请查看 包含zlib的Python3.6.15构建方法 ，结合本文的详细步骤使用。

---

> 使用的话直接下载 <https://cloud.tsinghua.edu.cn/f/760cb0bff40144e6b460/>
> 
> 如果要照着一步一步走的话推荐先看完再汇总一下  因为有些步骤是可以提前做的

---

> Ubuntu 22.04 成功编译

首先确保安装了 `aarch64-himix200-linux` 交叉编译工具链。

## 使用脚本进行构建

参考这个仓库，<https://github.com/karthickai/python-arm-xcompile>，拿出其中的脚本：

`python_xcompile.sh`:

```sh
#!/bin/bash

TARGET_HOST="aarch64-himix200-linux" # 这一行有修改 换成HiLinux的编译工具链
ROOT_FILESYSTEM="/usr/arm-linux-gnueabi/"
BUILD_HOST="x86_64-linux-gnu" # find out with uname -m
WORKING_DIRECTORY="python_xcompile"
INSTALL_DIRECTORY="$WORKING_DIRECTORY/_install"
PYTHON_VERSION="3.6.8"
SOURCE_DIRECTORY="Python-$PYTHON_VERSION"
PYTHON_ARCHIVE="Python-$PYTHON_VERSION.tar.xz"
ENABLE_MODULES="array cmath binascii _collections cPickle cStringIO datetime
_elementtree fcntl _functools itertools _io math _md5 operator _random select
_sha _socket _struct termios time unicodedata" # 这里需要删掉zlib  不然会报错 `./Modules/zlibmodule.c:10:10: fatal error: zlib.h: No such file or directory`

# Preparing compile environment
export RFS="$ROOT_FILESYSTEM"
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
./configure --host=$TARGET_HOST --build=$BUILD_HOST --prefix=$PREFIX \
    --disable-ipv6 --enable-unicode=ucs4 \
    ac_cv_file__dev_ptmx=no ac_cv_file__dev_ptc=no \
    ac_cv_have_long_long_format=yes
make
make install
```

```sh
$ vim compile_python.sh
# 把上面的内容复制进去保存退出
$ chmod +x compile_python.sh
$ ./compile_python.sh
```

## 报错没找到make

会报错没找到 `make`，装一下：

```sh
$ sudo apt-get install build-essential
```

## 再次构建

删除刚才的文件再次构建

```
$ rm -rf python_xcompile && ./compile_python.sh
...
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

经过漫长等待报错说没找到python。这是因为交叉编译需要本机安装对应版本的Python...

### 注意Ubuntu默认安装的Python版本

```sh
$ which python
$ which python3
/usr/bin/python3
$ python3 -V
Python 3.10.6
$ ls /usr/bin | grep python
python3
python3.10
```

我这里使用的是 Ubuntu 22.04，默认安装的 Python 是 3.10.6，不符合要求。我们安装 Python 3.6.8 到本机即可。

### 手动安装Python

**注意这里一定不要用miniconda/anaconda**，因为 conda 对 `python -V` 的输出是 `Python 3.6.8 :: Anaconda, Inc.`，不是 Python 默认的标准输出。会报错：

```
File "/home/yxj/Desktop/python_xcompile/Python-3.6.8/Lib/platform.py", line 1225, in _sys_version
    repr(sys_version))
ValueError: failed to parse CPython sys.version: '3.6.8 |Anaconda, Inc.| (default, Dec 30 2018, 01:22:34) \n[GCC 7.3.0]'
make: *** [Makefile:1102: install] Error 1
```

使用源码安装的方式，因为 Ubuntu 22.04 已经不支持一键安装 Python 3.6 了。

```sh
$ wget https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tar.xz
$ tar xvf Python-3.6.8.tar.xz
$ cd Python-3.6.8/
$ ./configure
$ sudo make altinstall
报错
zipimport.ZipImportError: can''t decompress data; zlib not available
make: *** [Makefile:1112: altinstall] Error 1

$ sudo apt-get install zlib1g-dev # 提前做这一步上一步就不会报错了
$ sudo make altinstall
......
Collecting setuptools
Collecting pip
Installing collected packages: setuptools, pip
Successfully installed pip-18.1 setuptools-40.6.2
```

确认本机已经安装Python3.6.8：

```sh
$ which python3.6
/usr/local/bin/python3.6
$ ls /usr/local/bin | grep python
python3.6
python3.6m # 不用管这个带m的版本 用python3.6就行
python3.6m-config
$ python3.6 -V
Python 3.6.8
$ python3.6
Python 3.6.8 (default, Nov  7 2022, 11:31:14) 
[GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## 再次构建

```
$ rm -rf python_xcompile && ./compile_python.sh
Looking in links: /tmp/tmp_mdm9wm6
Requirement already up-to-date: setuptools in /usr/local/lib/python3.6/site-packages (40.6.2)
Requirement already up-to-date: pip in /usr/local/lib/python3.6/site-packages (18.1)
```

## 拿到交叉编译的Python程序

```sh
$ cd python_xcompile/
$ tar -cf python368_hilinux.tar _install
```

将 python368_hilinux.tar 用ftp传到 HiLinux 上。

```sh
$ tar -xf python368_hilinux.tar
$ mv _install python3.6
$ cd python3.6/bin
$ ls
2to3               pydoc3             python3.6          pyvenv
2to3-3.6           pydoc3.6           python3.6-config   pyvenv-3.6
idle3              python3            python3.6m
idle3.6            python3-config     python3.6m-config
```

自己选择是否添加环境变量

## 测试Python可用

### 命令行解释

```sh
~/yxj/SOFTWARE/python3.6/bin $ ./python3.6
Python 3.6.8 (default, Nov  7 2022, 11:41:29)
[GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hello, world!")
Hello, world!
>>> 3**4
81
>>> 1.23+123
124.23
>>> import time
>>> time.time()
4577.1051597969999
>>> time.time()
4580.1884561320003
>>>
```

### 脚本

```py
def fibonacci(n):
    if n == 1 or n == 2:
        return 1
    return fibonacci(n - 1) + fibonacci(n - 2)

x = int(input("input N="))
print(f"f({x})={fibonacci(x)}")
```

```sh
$ vi hello.py
$ /root/yxj/SOFTWARE/python3.6/bin/python3.6 hello.py
input N=10
f(10)=55
```
