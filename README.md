# STM32 HAL 学习项目

基于 STM32F103C8T6 的 HAL 库每日练习记录，从基础 GPIO 到高级外设的完整学习路线。

## 项目简介

本项目收录了 STM32F103C8T6 使用 STM32 HAL 库进行开发的各类实验项目，涵盖从基础 GPIO 操作到定时器、PWM、外部中断、串口通信等外设的使用，适合 STM32 初学者学习参考。

## 硬件平台

- **MCU**: STM32F103C8T6 (Cortex-M3, 72MHz)
- **开发环境**: Keil uVision 5
- **HAL 库**: STM32F1xx_HAL_Driver
- **系统时钟**: 外部 HSE 8MHz，PLL 9 倍频至 72MHz

## 项目列表

### 1. [1]EXTI_test - GPIO 与外部中断实验

**功能说明**：
- 按键控制 LED（PA0 → PC13）
- 外部中断触发蜂鸣器（PB0 → PB1）
- 外部中断控制 LED（PB0 → PC13）

**硬件连接**：
- PC13: 板载 LED
- PA0: 按键输入（上拉）
- PB0: 外部中断输入（下降沿触发）
- PB1: 蜂鸣器输出

**项目文档**: [readme.txt](file:///c:/Users/1/Desktop/stm32_hal - 副本/[1]EXTI_test/Doc/readme.txt)

### 2. TIM_EXTI_test - 定时器与 PWM 实验

**功能说明**：
- TIM2 定时器中断（1 秒翻转 PC13 LED）
- TIM2 PWM 输出（PA0 呼吸灯效果）

**硬件连接**：
- PC13: 板载 LED（定时器中断指示）
- PA0: 外部 LED（PWM 呼吸灯）

**TIM2 参数**：
- Prescaler: 71
- Period: 99
- PWM 频率: 10kHz
- PWM 分辨率: 100 级

**项目文档**: [readme.txt](file:///c:/Users/1/Desktop/stm32_hal - 副本/TIM_EXTI_test/Doc/readme.txt)

### 3. UART_test - 串口通信实验

**功能说明**：

#### 实验 6：UART 基本收发实验
- 接收单个字符并回显到串口
- 使用 HAL_UART_Receive_IT 实现中断接收

#### 实验 7：串口指令控制外设
通过串口发送指令控制 LED 和蜂鸣器：
- 发送 `1`: 点亮 LED（PC13）
- 发送 `2`: 熄灭 LED（PC13）
- 发送 `3`: 开启蜂鸣器（PB1）
- 发送 `4`: 关闭蜂鸣器（PB1）

**硬件连接**：
| 引脚 | 功能 | 说明 |
|------|------|------|
| PA9 | USART1 TX | 连接 USB-TTL 的 RXD |
| PA10 | USART1 RX | 连接 USB-TTL 的 TXD |
| PC13 | LED | 板载 LED，低电平点亮 |
| PB1 | BEEP | 蜂鸣器控制引脚 |

**串口参数**：
- 波特率: 115200
- 数据位: 8
- 停止位: 1
- 校验位: 无

**项目文档**: [readme.txt](Doc/readme.txt)

## 项目结构

```
stm32_hal/
├── [1]EXTI_test/          # GPIO 与外部中断实验
│   ├── Core/              # 核心代码
│   │   ├── Inc/           # 头文件
│   │   └── Src/           # 源文件
│   ├── Drivers/           # HAL 驱动库
│   ├── MDK-ARM/           # Keil 工程文件
│   └── Doc/               # 文档
│
├── TIM_EXTI_test/         # 定时器与 PWM 实验
│   ├── Core/              # 核心代码
│   ├── Drivers/           # HAL 驱动库
│   ├── MDK-ARM/           # Keil 工程文件
│   └── Doc/               # 文档
│
└── UART_test/             # 串口通信实验
    ├── Core/              # 核心代码
    │   ├── Inc/           # 头文件
    │   └── Src/           # 源文件
    ├── Drivers/           # HAL 驱动库
    ├── MDK-ARM/           # Keil 工程文件
    └── Doc/               # 文档
```

## 快速开始

1. **安装开发环境**
   - Keil uVision 5
   - STM32CubeMX（可选，用于代码生成）
   - ST-Link 驱动

2. **编译项目**
   - 使用 Keil 打开对应项目的 `.uvprojx` 文件
   - 点击编译按钮（F7）

3. **烧录程序**
   - 连接 ST-Link 调试器
   - 点击下载按钮（F8）

4. **观察现象**
   - 参考各项目的文档说明

## 学习路线

- [x] 基础 GPIO 操作
- [x] 按键输入与消抖
- [x] 外部中断（EXTI）
- [x] 定时器（TIM）
- [x] PWM 输出
- [x] 串口通信（UART）
- [ ] ADC 采集
- [ ] I2C 通信
- [ ] SPI 通信
- [ ] FreeRTOS

## 学习要点

### UART 串口通信要点
1. **中断回调原则**：不要在中断回调中使用 `HAL_Delay()`，应在主循环中处理延时
2. **串口接线**：TX ↔ RX 交叉连接，必须共地
3. **printf 重定向**：通过重写 `fputc` 函数实现串口打印
4. **中断重启**：中断回调结束后需重新开启接收中断

### 常见问题与解决方法
- **串口无数据输出**：检查时钟配置（外部晶振是否工作）
- **接线接反**：确保 TX→RX、RX→TX 交叉连接
- **蜂鸣器一直响**：避免在中断回调中使用 `HAL_Delay()`

## 注意事项

1. 所有项目均使用外部 8MHz 晶振
2. PC13 LED 低电平点亮
3. 按键输入建议使用上拉配置
4. PWM 用于 LED 调光时，频率需 > 50Hz 以避免闪烁
5. 串口通信时必须共地

## 许可证

本项目仅供学习参考使用。
