# 单张图片处理

该项目的目的是从用户的电脑上读取一张图片，将其通过网络传输至HiLinux，HiLinux进一步将数据和指令传到FPGA，FPGA执行YOLOv3算法，得到识别结果传回HiLinux，通过网络传回用户电脑，在用户电脑上呈现识别结果。

## 整体架构

### HiLinux/FPGA 常驻程序

- FPGA 随时处于就绪状态，能够随时执行YOLOv3算法
    - 先使用 Vivado Program Device 烧录设备
    - 再 `reboot` HiLinux
- HiLinux 开启一个 HTTP server，监听 PC 传输图片数据
    - 注：需要先加载 xdma 内核才能调用 xdma 的库——`insmod xdma.ko`

### 整体网络传输

1. PC 从文件系统中读取一张图片(JPEG/PNG)到内存，记录图片尺寸(HxW)
2. PC 将图片数据转为 608x608 RGB8 的像素数组
    1. 将长的一边拉伸为608，另一边等比例缩放
    2. 再将不足608的边左右/上下对称添加黑边
3. PC 使用 PNG格式 无损压缩像素数组(608x608 RGB8)得到 PNG文件
4. PC 通过 HTTP(JSON) 传输 PNG文件 的 base64编码
5. HiLinux 对 PNG文件 的 base64编码 解码得到像素数组
6. HiLinux 将像素数组通过 PCIE 传输到 FPGA 中
7. FPGA 执行 YOLOv3算法
8. HiLinux 通过 PCIE 取回 FPGA 算法执行结果
9. HiLinux 将执行结果通过 HTTP(JSON) 传回 PC
10. PC 将识别结果拉伸到原图片尺寸(HxW)
11. PC 在像素数组(HxW)上框出识别的物体，并将其呈现在屏幕
12. PC 将像素数组(HxW)生成 PNG文件 保存到文件系统

### 结构体定义

#### PNG图片

使用PNG标准生成的图片，不使用interlace。

#### 像素数组

宽608高608的图片，共有608x608个像素，一维像素数组的长度为608x608x3，依次按行扫描存储R分量、G分量、B分量矩阵。

注：`Python - bytes` `C - char[]`

#### 识别结果

```c
struct RecognitionResult {
    count: uint32 // 识别结果的数量
    items: [Item] // 识别出的物体的数组
}

struct Item {
    top_left_x: uint32
    top_left_y: uint32
    width: uint32
    height: uint32
    label: char[32]
}
```
