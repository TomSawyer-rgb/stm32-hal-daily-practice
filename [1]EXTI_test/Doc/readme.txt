daily1_STM32 项目说明文档
===========================

项目概述
--------
项目名称：daily1_STM32
芯片型号：STM32F103C8
开发环境：Keil MDK-ARM
HAL库版本：STM32F1xx HAL Driver

项目功能
--------
本项目是一个GPIO实验程序，包含以下三个实验功能：

1. 按键控制LED（实验一）
   - 硬件连接：PA0作为按键输入，PC13作为LED输出
   - 实现功能：按下PA0按键，控制PC13 LED的亮灭状态切换
   - 消抖处理：使用10ms软件消抖，避免按键抖动导致误触发

2. 外部中断触发蜂鸣器（实验二）
   - 硬件连接：PB1作为蜂鸣器控制输出
   - 实现功能：当beep_flag标志位置位时，蜂鸣器鸣叫500ms
   - 当前状态：此功能已定义但默认未启用，可在中断回调中开启

3. 外部中断控制LED（实验三）
   - 硬件连接：PB0作为外部中断输入，PC13作为LED输出
   - 实现功能：PB0外部中断触发时，翻转PC13 LED状态
   - 中断配置：下降沿触发，带30ms消抖处理

硬件配置
--------
时钟配置：
  - 使用外部高速时钟（HSE）：8MHz
  - PLL倍频：9倍
  - 系统时钟（SYSCLK）：72MHz
  - AHB时钟：72MHz
  - APB1时钟：36MHz（2分频）
  - APB2时钟：72MHz

GPIO引脚配置：
  - PC13（LED）：推挽输出，低速，上电默认高电平（LED熄灭）
  - PA0（按键）：输入，上拉
  - PB0（外部中断）：外部中断，下降沿触发，上拉
  - PB1（蜂鸣器）：推挽输出，低速，上电默认高电平（蜂鸣器关闭）

中断配置
--------
EXTI0_IRQn：PB0外部中断
  - 优先级：0（最高）
  - 触发方式：下降沿触发
  - 消抖时间：30ms

软件架构
--------
主程序流程（main.c）：
  1. 系统初始化（HAL_Init）
  2. 配置系统时钟（SystemClock_Config）
  3. 初始化GPIO外设（MX_GPIO_Init）
  4. 主循环：
     - 检测PA0按键状态，控制LED翻转
     - 检测beep_flag标志，控制蜂鸣器
     - 检测LED_flag标志，控制LED翻转

中断处理（HAL_GPIO_EXTI_Callback）：
  - 响应PB0外部中断
  - 消抖判断（30ms间隔）
  - 设置LED_flag标志

GPIO配置（gpio.c）：
  - 使能GPIO时钟（GPIOC、GPIOD、GPIOA、GPIOB）
  - 配置各GPIO引脚的工作模式和参数
  - 配置EXTI0中断优先级并使能

引脚定义（main.h）：
  - LED_PC13_Pin：PC13
  - Key_PA0_Pin：PA0
  - EXIT_PB0_Pin：PB0
  - Beep_PB1_Pin：PB1

编译说明
--------
编译工具：Keil MDK-ARM
输出文件：
  - daily1_STM32.axf：ELF格式可执行文件
  - daily1_STM32.hex：HEX格式，可用于编程器烧写
  - daily1_STM32.map：内存映射文件

注意事项
--------
1. 消抖处理：按键和外部中断均实现了消抖机制，避免误触发
2. LED极性：PC13 LED低电平点亮，高电平熄灭
3. 蜂鸣器极性：PB1蜂鸣器低电平触发，高电平关闭
4. 中断安全：中断回调函数中使用标志位与主循环通信，避免复杂操作
5. 时钟配置：使用外部8MHz晶振，PLL 9倍频得到72MHz系统时钟

文件结构
--------
Core/
  Inc/
    gpio.h：GPIO配置头文件
    main.h：主程序头文件，包含引脚定义
    stm32f1xx_hal_conf.h：HAL库配置文件
    stm32f1xx_it.h：中断处理头文件
  Src/
    gpio.c：GPIO初始化配置
    main.c：主程序
    stm32f1xx_hal_msp.c：HAL库外设初始化
    stm32f1xx_it.c：中断处理函数

Drivers/
  CMSIS/：ARM Cortex-M处理器核心头文件
  STM32F1xx_HAL_Driver/：STM32F1系列HAL驱动库

MDK-ARM/
  daily1_STM32.uvprojx：Keil工程文件
  daily1_STM32/：编译输出目录

Doc/
  readme.txt：本说明文档
