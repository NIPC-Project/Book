# 视频+直通

> Work in Progress

基于 pcie703SdiCameraDDRDemo

这个工程将摄像头接入 FPGA，然后将视频流数据不加任何处理传至 HiLinux，HiLinux 再传至 PC，PC 在屏幕上持续显示摄像头的画面。

**注**

- 现阶段只考虑单 PC 查看摄像头实时的内容，不考虑单对多的直播。
- 视频流可以被理解为时间上连续的多张单帧图像，也就是暂时不考虑视频编码。

## 摄像头

使用的摄像头为 [SDI摄像头工业设备监控教学场室录播演播视频会议3G SDI摄像机](https://m.tb.cn/h.U6t0ZPB?tk=N5s2dcaIbkv)，视频帧率：PAL-1080P25帧，NTSC-1080P30帧。

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

TODO 介绍如何使用 SDI 转 HDMI 的东西确保摄像头正常工作。
