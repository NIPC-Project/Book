# 交叉编译需要用到的库

## 编译

```sh
$ cd $HOME/Desktop/HiLinux
```

### zlib

`compile-zlib.sh`:

```sh
#!/bin/bash
ROOT_DIR="$HOME/Desktop/HiLinux"
INSTALL_DIR="${ROOT_DIR}/libraries"
mkdir -p $ROOT_DIR $INSTALL_DIR

wget https://zlib.net/zlib1213.zip
unzip zlib1213.zip
cd zlib-1.2.13/
export cross=aarch64-himix200-linux
CHOST="${cross}" CC="${cross}-gcc" CXX="${cross}-g++" AR="${cross}-ar" LD="${cross}-ld" RANLIB="${cross}-ranlib" ./configure --prefix=$INSTALL_DIR
make -j4
make install
cd ..

rm -rf zlib1213.zip zlib-1.2.13/
```

```sh
$ vim compile-zlib.sh
$ chmod +x compile-zlib.sh
$ ./compile-zlib.sh
```

这样在 `INSTALL_DIR` 中，就已经安装了交叉编译好的 `zlib`：

```sh
$ tree libraries/
libraries/
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

### ffi

```sh
#!/bin/bash
ROOT_DIR="$HOME/Desktop/HiLinux"
INSTALL_DIR="${ROOT_DIR}/libraries"
mkdir -p $ROOT_DIR $INSTALL_DIR

wget https://github.com/libffi/libffi/releases/download/v3.4.4/libffi-3.4.4.tar.gz
tar xf libffi-3.4.4.tar.gz
cd libffi-3.4.4/
export cross=aarch64-himix200-linux
CHOST="${cross}" CC="${cross}-gcc" CXX="${cross}-g++" AR="${cross}-ar" LD="${cross}-ld" RANLIB="${cross}-ranlib" ./configure --prefix=$INSTALL_DIR --host=${cross}
make -j4
make install
cd ..

rm -rf libffi-3.4.4.tar.gz libffi-3.4.4/
```

```sh
$ vim compile-ffi.sh
$ chmod +x compile-zlib.sh
$ ./compile-zlib.sh
```

这样在 `INSTALL_DIR` 中，就已经安装了交叉编译好的 `libffi`：

```sh
$ tree libraries/
libraries/
├── include
│   ├── ffi.h
│   ├── ffitarget.h
│   ├── zconf.h
│   └── zlib.h
├── lib
│   ├── libz.a
│   ├── libz.so -> libz.so.1.2.13
│   ├── libz.so.1 -> libz.so.1.2.13
│   ├── libz.so.1.2.13
│   └── pkgconfig
│       ├── libffi.pc
│       └── zlib.pc
├── lib64
│   ├── libffi.a
│   ├── libffi.la
│   ├── libffi.so -> libffi.so.8.1.2
│   ├── libffi.so.8 -> libffi.so.8.1.2
│   └── libffi.so.8.1.2
└── share
    ├── info
    │   ├── dir
    │   └── libffi.info
    └── man
        └── man3
            ├── ffi.3
            ├── ffi_call.3
            ├── ffi_prep_cif.3
            ├── ffi_prep_cif_var.3
            └── zlib.3
```
