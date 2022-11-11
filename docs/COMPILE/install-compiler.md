# Ubuntu安装HiLinux编译工具链

首先从最简单的 C/C++ 入手。我们需要先安装 gcc/g++。

## 使用海思提供的交叉编译工具

### 1. 有一台Ubuntu的机器

打开 <https://ubuntu.com/download/desktop>，下载最新的LTS版本即可。221104看到的是 `Ubuntu 22.04.1 LTS`。我这里用macOS上的虚拟机VMWare创建一个新的虚拟机，用户名是 `yxj`。

### 2. 安装必要依赖

由于所给的工具链是32位，需要先安装依赖（参考<https://askubuntu.com/questions/454253/how-to-run-32-bit-app-in-ubuntu-64-bit>）：

```sh
$ sudo dpkg --add-architecture i386
$ sudo apt-get update
$ sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386
```

### 3. 下载aarch64-himix200-linux.tgz

这份压缩包里面有在x86系统上交叉编译适用于HiLinux系统的程序，随海思产品附。清华云盘下载链接：<https://cloud.tsinghua.edu.cn/f/3a165bbecdae43078723/>

### 4. 解压安装

将压缩包放到Ubuntu系统

```sh
$ tar -xf aarch64-himix200-linux.tgz
$ cd aarch64-himix200-linux
$ sudo ./aarch64-himix200-linux.install
```

输入账户密码即可安装。

修改环境路径：

```sh
$ vim ~/.bashrc # 没有vim跑一下`sudo apt-get install vim`
```

最后一行加入：

```
export PATH="$PATH:/opt/hisi-linux/x86-arm/aarch64-himix200-linux/bin"
```

保存退出。

```sh
$ source ~/.bashrc
```

### 5. 确认安装情况

```sh
$ aarch64-himix200-linux-gcc --version
aarch64-himix200-linux-gcc (HC&C V1R3C00SPC200B017_20190910) 7.3.0
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

创建下面的C文件`main.c`：

```c
#include <stdio.h>

int main() {
  printf("Hello, world.\n");
  return 0;
}
```

```sh
$ aarch64-himix200-linux-gcc main.c
/opt/hisi-linux/x86-arm/aarch64-himix200-linux/host_bin/../libexec/gcc/aarch64-linux-gnu/7.3.0/cc1: error while loading shared libraries: libz.so.1: cannot open shared object file: No such file or directory
$ sudo apt-get install zlib1g:i386
$ aarch64-himix200-linux-gcc main.c
```

将生成的 `a.out` 用ftp传到板子上：

```
~/yxj/SOFTWARE # ./a.out
-sh: ./a.out: Permission denied
~/yxj/SOFTWARE # chmod +x ./a.out
~/yxj/SOFTWARE # ./a.out
Hello.~/yxj/SOFTWARE #
```

可以看到 `Hello.` 正确被输出。

### 查看所有可用程序

```
$ ls /opt/hisi-linux/x86-arm/aarch64-himix200-linux/bin
aarch64-himix200-linux-addr2line   aarch64-himix200-linux-gcov-tool
aarch64-himix200-linux-ar          aarch64-himix200-linux-gdb
aarch64-himix200-linux-as          aarch64-himix200-linux-gprof
aarch64-himix200-linux-c++         aarch64-himix200-linux-ld
aarch64-himix200-linux-c++filt     aarch64-himix200-linux-ld.bfd
aarch64-himix200-linux-cpp         aarch64-himix200-linux-nm
aarch64-himix200-linux-elfedit     aarch64-himix200-linux-objcopy
aarch64-himix200-linux-g++         aarch64-himix200-linux-objdump
aarch64-himix200-linux-gcc         aarch64-himix200-linux-ranlib
aarch64-himix200-linux-gcc-7.3.0   aarch64-himix200-linux-readelf
aarch64-himix200-linux-gcc-ar      aarch64-himix200-linux-size
aarch64-himix200-linux-gcc-nm      aarch64-himix200-linux-strings
aarch64-himix200-linux-gcc-ranlib  aarch64-himix200-linux-strip
aarch64-himix200-linux-gcov
```
