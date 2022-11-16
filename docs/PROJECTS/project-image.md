# 单张图像处理

## 整体架构

1. 电脑(client)通过HTTP协议传输需要处理的PNG图片(HxW, rgb8 or rbga8)到HiLinux(server)
1. HiLinux对PNG图片(HxW)解码得到所有的像素数组(HxW)进一步转为像素数组(608x608, rgb8)
1. HiLinux将像素数组(608x608)传到PCIE从而传到FPGA
1. FPGA对图像通过YOLO网络进行识别
1. FPGA把识别结果的数组通过PCIE传回HiLinux
1. HiLinux将YOLO尺寸(608x608)的识别结果通过转为符合原始图像尺寸(HxW)的识别结果
1. HiLinux在像素数组(HxW)上框出识别的物体
1. HiLunux将像素数组(HxW)生成PNG通过HTTP协议传回电脑(client)

### 结构体定义

#### PNG图片

使用PNG标准生成的图片，不使用interlace。

#### 像素数组

宽608高608的图像，共有608x608个像素，一维像素数组的长度为608x608x3，每相邻的三个值表示对应位置像素的rgb三个分量。

#### 识别结果

```
struct {
  top_left_x: uint32
  top_left_y: uint32
  width: uint32
  height: uint32
  label: String
}
```

## 代码仓库

- https://github.com/NIPC-Project/python-png
    - 电脑和开发板之间通过Python搭建的HTTP协议传PNG文件
    - 画框和label
