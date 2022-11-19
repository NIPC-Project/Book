# Python调用C语言动态库

首先使用者需要有关于 C语言 动态库 (.so) 的知识。可以查看 <https://docencia.ac.upc.edu/FIB/USO/Bibliografia/unix-c-libraries.html>。

示例代码请查看: <https://github.com/NIPC-Project/python-call-c> 其中 `C/Makefile` 涵盖了所有的步骤

在 Python 中调用 C语言 动态库 主要使用 Python 的 ctypes 这个库。官方文档 <https://docs.python.org/3.10/library/ctypes.html>。

## 以调用Xilinx官方的PCIE控制库为例

查看 <https://github.com/NIPC-Project/pcie-image> `pcie-lib` 文件夹。

声明 `XPcie_Lib.h`：

```c
//板卡信息
#define MAX_CARDNUM 8
typedef struct{
	int nBoardNum;
	struct {
		unsigned short vendor;
		unsigned short device;
		int nBoardID;
	} BOARDINDEX[MAX_CARDNUM];
} XPCIE_BOARDINFO, *PXPCIE_BOARDINFO;

void XPcie_GetBoardListInfo(PXPCIE_BOARDINFO pXPcieBoardInfo);
```

Python 调用（其中 `lib/libxdma_so.so` 为上述头文件对应的库）：

```py
import ctypes


class PcieBoard(ctypes.Structure):
    _fields_ = [
        ("vendor", ctypes.c_uint16),
        ("device", ctypes.c_uint16),
        ("boardId", ctypes.c_int32),
    ]


class PcieBoardsInfo(ctypes.Structure):
    _fields_ = [("boardNum", ctypes.c_int32), ("boards", PcieBoard * 8)]


pcieBoardsInfo = PcieBoardsInfo()
print(ctypes.sizeof(pcieBoardsInfo))
print(pcieBoardsInfo.boardNum)


libc_xdma_so = ctypes.cdll.LoadLibrary("./lib/libxdma_so.so")
libc_xdma_so.XPcie_GetBoardListInfo(ctypes.byref(pcieBoardsInfo))
```
