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
