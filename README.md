# STM32 HAL 学习项目

基于 STM32F103C8T6 的 HAL 库每日练习记录，从基础 GPIO 到高级外设的完整学习路线。

## 项目简介

本项目收录了 STM32F103C8T6 使用 STM32 HAL 库进行开发的各类实验项目，涵盖从基础 GPIO 操作到定时器、PWM、外部中断等外设的使用，适合 STM32 初学者学习参考。

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

## 项目结构

```
stm32_hal/
├── [1]EXTI_test/          # GPIO 与外部中断实验
│   ├── Core/              # 核心代码
│   │   ├── Inc/           # 头文件
│   │   └── Src/           # 源文件
│   ├── Drivers/           # HAL 驱动库
│   │   ├── CMSIS/         # ARM CMSIS
│   │   └── STM32F1xx_HAL_Driver/
│   ├── MDK-ARM/           # Keil 工程文件
│   └── Doc/               # 文档
│
└── TIM_EXTI_test/         # 定时器与 PWM 实验
    ├── Core/              # 核心代码
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
- [ ] 串口通信（UART）
- [ ] ADC 采集
- [ ] I2C 通信
- [ ] SPI 通信
- [ ] FreeRTOS

## 注意事项

1. 所有项目均使用外部 8MHz 晶振
2. PC13 LED 低电平点亮
3. 按键输入建议使用上拉配置
4. PWM 用于 LED 调光时，频率需 > 50Hz 以避免闪烁

## 许可证

本项目仅供学习参考使用。
