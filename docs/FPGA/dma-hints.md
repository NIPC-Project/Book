# DMA使用说明

> 注意所有的内存、地址单位都是 Byte！没有特例！

首先 FPGA 是有一份内存的，在编写 HiLinux 这边的程序的时候，不需要考虑 FPGA 的部分，因为这部分内存只有 FPGA 才能访问得到。这部分内存的正式名字应该是 DMA。

我们需要先使用 `int _aligned_malloc(void **memptr, size_t size);` 来申请 HiLinux 和 FPGA 能够共同访问的内存。考虑到输入输出都可以使用这个共享内存，我们取两者的最大值初始化这片内存就好了。比如传 `2 * 4096 * 4096 B` 的数据，`size` 给这个值就可以了。

在将数据从 HiLinux 写入到 FPGA 的 DMA 时，我们使用的函数是 `int XPcie_DMA_Write(int fd, uint32_t addr, char *buffer, size_t size);`。这里的 `buffer` 需要给入共享内存。

在将数据从 FPGA 的 DMA 读取到 HiLinux 的内存中时，我们使用的函数是 `int XPcie_DMA_Read(int fd, uint32_t addr, char *buffer, size_t size);`，这里的 `buffer` 需要给入共享内存。

读取之后，HiLinux 程序可以直接读取共享内存中的数据，这就是 FPGA 返回的处理结果存储的地方。

程序结束时，需要注意使用 `void _aligned_free(void *buf);` 释放共享内存

## 附 XPcie_Lib.h

```c
#include <unistd.h>
#include <assert.h>
#include <fcntl.h>
#include <getopt.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#ifdef XPCIE_DLL_EXPORTS
#define XPCIE_DLL_API
#else
#define XPCIE_DLL_API
#endif

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

#ifdef __cplusplus
extern "C" {
#endif
	/*-
	Function name     : _aligned_malloc
	Description       : 申请通道缓冲区
	Parameter
	@ memptr 		  : 缓冲区指针
	@ size			  : 缓冲区大小
	* Return          : 失败返回<0
	-*/
	int _aligned_malloc(void **memptr, size_t size);

	/*-
	Function name     : _aligned_free
	Description       : 释放通道缓冲区
	Parameter
	@ buf 		  	  : 缓冲区指针
	* Return          : 
	-*/
	void _aligned_free(void *buf);

	/*-
	Function name     : XPcie_GetBoardListInfo
	Description       : 获取板卡列表信息
	Parameter
	@ pXPcieBoardInfo : 板卡信息
	* Return          : 
	-*/
	XPCIE_DLL_API void XPcie_GetBoardListInfo(PXPCIE_BOARDINFO pXPcieBoardInfo);

	/*-
	Function name : XPcie_Device_Open
	Description   : 打开板卡
	Parameter
	@ fd		  : 板卡号
	* Return      : 成功返回要打开的板卡号，失败返回-1
	-*/
	XPCIE_DLL_API int XPcie_Device_Open(int fd);

	/*-
	Function name : XPcie_Device_Close
	Description   : 关闭板卡
	Parameter
	@ fd		  : 板卡号
	* Return      : 成功返回0
	-*/
	XPCIE_DLL_API int XPcie_Device_Close(int fd);
	
	/*-
	Function name : XPcie_DMA_Read_Init
	Description   : 接收通道初始化
	Parameter
	@ fd		  : 板卡号
	@ size		  : DMA大小
	* Return      : 成功返回0，否则<0
	-*/
	XPCIE_DLL_API int XPcie_DMA_Read_Init(int fd, size_t size);

	/*-
	Function name : XPcie_DMA_Read
	Description   : 接收通道传输数据
	Parameter
	@ fd		  : 板卡号
	@ addr  	  : DDR地址(未使用,可随意传)
	@ buffer	  : 上行数据接收缓冲区
	@ size		  : DMA大小
	* Return      : 成功返回传输数据长度，否则<0
	-*/
	XPCIE_DLL_API int XPcie_DMA_Read(int fd, uint32_t addr, char *buffer, size_t size);

	/*-
	Function name : XPcie_DMA_Write_Init
	Description   : 接收通道初始化
	Parameter
	@ fd		  : 板卡号
	@ size		  : DMA大小
	* Return      : 成功返回0，否则<0
	-*/
	XPCIE_DLL_API int XPcie_DMA_Write_Init(int fd, size_t size);
	
	/*-
	Function name : XPcie_DMA_Write
	Description   : 发送通道传输数据
	Parameter
	@ fd		  : 板卡号
	@ addr  	  : DDR地址(未使用,可随意传)
	@ buffer	  : 下行数据接收缓冲区
	@ size		  : DMA大小
	* Return      : 成功返回传输数据长度，否则<0
	-*/
	XPCIE_DLL_API int XPcie_DMA_Write(int fd, uint32_t addr, char *buffer, size_t size);

	/*-
	未使用
	Function name : XPcie_Write_Reg
	Description   : 写寄存器
	Parameter
	@ fd		  : 板卡号
	@ addr		  : 寄存器地址
	@ value		  : 写入寄存器的值
	* Return      : 成功返回0，失败返回-1
	-*/
	XPCIE_DLL_API int XPcie_Write_Reg(int fd, long addr, unsigned int value);

	/*-
	未使用
	Function name : XPcie_Read_Reg
	Description   : 读取寄存器
	Parameter
	@ fd		  : 板卡号
	@ addr		  : 寄存器地址
	* Return      : 成功返回寄存器里的值，失败返回-1
	-*/
	XPCIE_DLL_API unsigned int XPcie_Read_Reg(int fd, long addr);
	
	/*-
	Function name : XPcie_Reset_Device
	Description   : FPGA复位
	Parameter
	@ fd		  : 板卡号
	* Return      :
	-*/
	XPCIE_DLL_API void XPcie_Reset_Device(int fd);
#ifdef __cplusplus
}
#endif  /* end of __cplusplus */
```
