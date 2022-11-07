# 安装程序

## 安装miniconda（已失败 不继续推进）

https://docs.conda.io/en/latest/miniconda.html  ->  Miniconda3 Linux-aarch64 64-bit

```
$ cd ~
$ mkdir yxj
$ mkdir software
$ chmod +x Miniconda3-latest-Linux-aarch64.sh
$ ./Miniconda3-latest-Linux-aarch64.sh
PREFIX=/root/miniconda3
Unpacking payload ...
/root/miniconda3/conda.exe: error while loading shared libraries: libz.so.1: cannot open shared object file: No such file or directory
/root/miniconda3/conda.exe: error while loading shared libraries: libz.so.1: cannot open shared object file: No such file or directory
```

查看 https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html 来了解关于 shared library 的信息

```
echo $LD_LIBRARY_PATH
/usr/local/lib:/usr/lib
```

在这个网站查找rpm包 <http://rpmfind.net/linux/rpm2html/search.php?query=magic&submit=Search+...&system=&arch=>

目前不行  还有别的错。按理来说都应该从源码使用交叉编译工具进行编译才能使用相关程序。
