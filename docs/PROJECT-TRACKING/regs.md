# 寄存器定义

- RW[0]
    - 写 1 再写 0：所有信号 rst
- RW[1]
    - 0：init 阶段
    - 1：update 阶段
- RW[2]
    - init 阶段首帧中框的中心（低 16 位 xc，高 16 位 yc）
- R[0]
    - 1：FPGA 对一帧的处理结束，rst 时初值为 0
- R[1]
    - FPGA 算出的 xc yc（低 16 位 xc，高 16 位 yc）
