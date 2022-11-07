# 编译安装程序

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

```
$ tar -xf aarch64-himix200-linux.tgz
$ cd aarch64-himix200-linux
$ sudo ./aarch64-himix200-linux.install
```

输入账户密码即可安装。

修改环境路径：

```
$ vim ~/.bashrc
```

最后一行加入：

```
export PATH="$PATH:/opt/hisi-linux/x86-arm/aarch64-himix200-linux/bin"
```

```
$ source ~/.bashrc
```

### 5. 确认安装情况

```
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
  printf("Hello.");
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

## 使用ARM官方的编译工具（未成功 留档）

查看 https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads ， 找到一个稳定版本（非beta）进行下载。

> The Cortex-A53 is the most widely deployed 64-bit Armv8-A processor.

板卡的ARM芯片是 ARM Cortex-A53，选择64位bare-metal进行下载。一个里面有这么几项：

- Windows (mingw-w64-i686) hosted cross toolchains
    - AArch64 GNU/Linux target (aarch64-none-linux-gnu)
        - [gcc-arm-11.2-2022.02-mingw-w64-i686-aarch64-none-elf.zip](https://developer.arm.com/-/media/Files/downloads/gnu/11.2-2022.02/binrel/gcc-arm-11.2-2022.02-mingw-w64-i686-aarch64-none-elf.zip?rev=e80d3e36dc2c4c3e99d885b03e4a2e42&hash=57D25E18E569B454C7FE3C124CF5C1F6)
- x86_64 Linux hosted cross toolchains
- AArch64 Linux hosted cross toolchains
    - AArch64 ELF bare-metal target (aarch64-none-elf)
        - [gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf.tar.xz](https://developer.arm.com/-/media/Files/downloads/gnu/11.2-2022.02/binrel/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf.tar.xz?rev=6999776f159f49cbb12166e86dacd6c2&hash=703D7E8481C11FA8043E66EBF947A983)
- macOS (x86_64) hosted cross toolchains
    - AArch64 bare-metal target (aarch64-none-elf)
        - [gcc-arm-11.2-2022.02-darwin-x86_64-aarch64-none-elf.tar.xz](https://developer.arm.com/-/media/Files/downloads/gnu/11.2-2022.02/binrel/gcc-arm-11.2-2022.02-darwin-x86_64-aarch64-none-elf.tar.xz?rev=633246d6f26945ec892020aac1d9c1bc&hash=CC53BEFEAFD913A10EB9FEB31CE9CF99)

下载之后解压在bin文件夹里面可以看到gcc，在include文件夹里面可以看到各种头文件。在编译时自己使用对应的工具就可以了。

### macOS（未成功）

未成功  使用gcc编译没有主体的c文件  exit.c拿到了本机的奇怪的地方的文件  然后无法成功编译

### HiLinux（未成功）

ftp传输进去 gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf.tar.xz 之后，用 `tar -xf gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf.tar.xz` 解压

gcc 路径为 `/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/aarch64-none-elf-gcc`

用最简单的main.c尝试编译，报错如下

```
~/yxj/CODE # /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bi
n/aarch64-none-elf-gcc main.c
/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/bin/ld: /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/lib/libc.a(lib_a-exit.o): in function `exit':
/data/jenkins/workspace/GNU-toolchain/arm-11/src/newlib-cygwin/newlib/libc/stdlib/exit.c:64: undefined reference to `_exit'
/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/bin/ld: /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/lib/libc.a(lib_a-sbrkr.o): in function `_sbrk_r':
/data/jenkins/workspace/GNU-toolchain/arm-11/src/newlib-cygwin/newlib/libc/reent/sbrkr.c:51: undefined reference to `_sbrk'
/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/bin/ld: /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/lib/libc.a(lib_a-writer.o): in function `_write_r':
/data/jenkins/workspace/GNU-toolchain/arm-11/src/newlib-cygwin/newlib/libc/reent/writer.c:49: undefined reference to `_write'
/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/bin/ld: /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/lib/libc.a(lib_a-closer.o): in function `_close_r':
/data/jenkins/workspace/GNU-toolchain/arm-11/src/newlib-cygwin/newlib/libc/reent/closer.c:47: undefined reference to `_close'
/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/bin/ld: /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/lib/libc.a(lib_a-fstatr.o): in function `_fstat_r':
/data/jenkins/workspace/GNU-toolchain/arm-11/src/newlib-cygwin/newlib/libc/reent/fstatr.c:55: undefined reference to `_fstat'
/root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/bin/ld: /root/yxj/SOFTWARE/gcc-arm-11.2-2022.02-aarch64-aarch64-none-elf/bin/../lib/gcc/aarch64-none-elf/11.2.1/../../../../aarch64-none-elf/lib/libc.a(lib_a-isattyr.o): in function `_isatty_r':
```
