# 存储与计算的模块连接

## 存储模块

### 只读

- 在 T0 输入 `dout_addr`，在 T1 输出 `dout`。

```
input  wire clk,
input  wire [DOUT_ADDR_WIDTH-1:0] dout_addr,
output wire [DOUT_WIDTH-1:0] dout
```

### A写B读

- 写
    - 输入 `din_valid` 为 1 时同周期输入 `din`，默认顺序读写，从零开始加加。
- `complete` 表示 blkmem 已经存满数据，能够进行下一步的操作（读出数据）了。
- 读
    - 在 T0 输入 `dout_addr`，在 T1 输出 `dout`。

```
input  wire clk,
input  wire rst,
input  wire din_valid,
input  wire [DIN_WIDTH-1:0] din,
output wire complete,
input  wire [DOUT_ADDR_WIDTH-1:0] dout_addr,
output wire [DOUT_WIDTH-1:0] dout
```

## 计算模块

- 计算模块的数据从一个存储模块而来，由计算模块给出 `blkmem_din_addr` 输入到存储模块，存储模块在下一个周期给出 `blkmem_din`。
- 给出计算结果时，需要存入一个存储模块，在 `dout` 有值的时候置 `dout_valid` 为 1 顺序写入另一个存储模块。

```
input  wire clk,
input  wire rst,
input  wire start,
output wire [DIN_ADDR_WIDTH-1:0] blkmem_din_addr,
input  wire [DIN_WIDTH-1:0] blkmem_din,
output wire dout_valid,
output wire [DOUT_WIDTH-1:0] dout
```
