# 流程

## HiLinux 和 FPGA 的数据交互

### init 阶段

| HiLinux | FPGA |
| - | - |
| RW[0] 写 1 再写 0 |  |
|  | 重置所有信号  准备开始一帧的处理 |
| RW[1] 写 0 |  |
| RW[2] 写 xc yc |  |
| WriteMemory 首帧 |  |
|  | 320x240 写满之后开始处理 |
|  | 处理结束（写 Blkmem_ab） |
|  | R[0] 写 1 |
| 等待直到 R[0] 为 1 |  |

### update 阶段

| HiLinux | FPGA |
| - | - |
| RW[0] 写 1 再写 0 |  |
|  | 重置所有信号  准备开始一帧的处理 |
| RW[1] 写 1 |  |
| WriteMemory 一帧 |  |
|  | 320x240 写满之后开始处理 |
|  | 处理结束（写 Blkmem_ab  R[1] 写 xc yc） |
|  | R[0] 写 1 |
| 如果图片处理完毕 break |  |
| 等待直到 R[0] 为 1 |  |
| 读 R[1] 得到 xc yc |  |

## FPGA 算法硬件实现

### init

- 【rx】传入 320x240 灰度 8 bit 的 frame
- 【cropfft】使用 xc, yc 拿到尺寸为 w, h 的 f；f = f - 0.5；f = f · window；F = FFT(f)
- $A = K · F^*$，$B = F · F^*$

### update

- 【rx】传入 320x240 灰度 8 bit 的 frame
- 计算偏移
    - 【cropfft】使用 xc, yc 拿到尺寸为 w, h 的 f；f = f - 0.5；f = f · window；F = FFT(f)
    - 【h】H = A / B
    - 【g】G = H · F
    - 【ifft】g = real(IFFT(G))
    - 【argmax】找到 g 中最大值的坐标、得到两帧间的偏移量更新 xc, yc
- 更新状态
    - 【cropfft】使用新的 xc, yc 拿到尺寸为 w, h 的 f；f = f - 0.5；f = f · window；F = FFT(f)
    - $A = \eta\ (K · F^*) + (1 - \eta)\ A$，$B = \eta\ (F · F^*) + (1 - \eta)\ B$
