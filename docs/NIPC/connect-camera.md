# 开发板连接摄像头

使用的摄像头为 [SDI摄像头工业设备监控教学场室录播演播视频会议3G SDI摄像机](https://m.tb.cn/h.U6t0ZPB?tk=N5s2dcaIbkv)，视频帧率：PAL-1080P25帧，NTSC-1080P30帧。

> 以下为循序渐进的连接过程

## 测试摄像头无问题

摄像头有三个接口，分别是：

- 红色 POWER
    - 电源
    - 接 DC12V 1A
- 绿色 HD-SDI
    - HD-SDI高清数字接口
    - BNC母接头
- 黄色 VIDEO（不使用）
    - CVBS模拟输出（符合SMPTE 292M标准）
    - BNC母接头

先使用  HD-SDI转HDMI的器材  将摄像头直连到屏幕上显示拍摄到的画面。

## 使用pcie703SdiCameraDDRDemo将摄像头连至FPGA

> WORK IN PROGRESS

安装 `Vivado 2018.3`，双击打开 `pcie703SdiCameraDDRDemo/prj/top.xpr`。

点击左侧 `PROGRAM AND DEBUG > Generate Bitstream`。

### 遇到的问题

#### 无License

![](images-connect-camera/license-error.png)

```
[Common 17-345] A valid license was not found for feature 'Synthesis' and/or device 'xcku060'. Please run the Vivado License Manager for assistance in determining
which features and devices are licensed for your system.
Resolution: Check the status of your licenses in the Vivado License Manager. For debug help search Xilinx Support for "Licensing FAQ". If you are using a license server, verify that the license server is up and running a version of the xilinx daemon that is compatible with the version of Xilinx software that you are using. Please note that Vivado 2017.3 and later requires upgrading your license server tools to the Flex 11.14.1 tools. Please confirm with your license admin that the correct version of the license server tools are installed.
```

可以试用也可以花钱买……
