# 编译安装程序

首先从最简单的 C/C++ 入手。我们需要先安装 gcc/g++。

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
