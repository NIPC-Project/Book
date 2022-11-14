# 单张图像处理

> TODO

## 整体架构

- 电脑通过HTTP协议传输需要处理的PNG图片和处理的指令传到HiLinux
- HiLinux将PNG图像的数据和操作指令传到PCIE从而传到FPGA
- FPGA对图像进行处理  把处理结果通过PCIE传回HiLinux
- HiLinux将图像数据生成为PNG通过网络传回HiLinux
