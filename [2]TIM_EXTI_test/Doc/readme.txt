TIM_EXTI_test 项目说明文档
==========================

一、项目概述
-----------
本项目是一个基于 STM32F103C8T6 的 TIM 定时器 + PWM 输出 + EXTI 外部中断的综合例程。

二、硬件配置
-----------
1. MCU: STM32F103C8T6 (Cortex-M3, 72MHz)
2. LED:
   - PC13: 板载 LED，用于定时器中断指示（1秒翻转一次）
   - PA0: 外部 LED，连接 TIM2_CH1 PWM 输出，实现呼吸灯效果

三、功能说明
-----------
1. TIM2 定时器中断
   - 每秒触发一次中断，翻转 PC13 LED 状态
   - 用途：系统运行指示

2. TIM2 PWM 输出
   - PA0 输出 PWM1 模式
   - 占空比在 0%~100% 之间平滑变化
   - 实现呼吸灯效果（渐亮→渐暗循环）

3. 系统时钟配置
   - 使用外部 HSE 晶振 (8MHz)
   - PLL 9倍频
   - SYSCLK = 72MHz

四、TIM2 参数配置
----------------
- Prescaler: 72-1 = 71
- Period: 100-1 = 99
- PWM 频率: 72MHz / 72 / 100 = 10kHz
- PWM 分辨率: 100 级 (0~99)
- 呼吸灯周期: ~1秒 (通过 HAL_Delay(5) + 步进控制)

五、引脚分配
-----------
- PA0 (TIM2_CH1): PWM 输出 → 外部 LED (呼吸灯)
- PC13: 板载 LED (定时器中断指示)

六、代码结构
-----------
Core/
├── Src/
│   ├── main.c          - 主程序入口、PWM 呼吸灯逻辑
│   ├── gpio.c          - GPIO 初始化
│   ├── tim.c           - TIM2 定时器和 PWM 配置
│   └── stm32f1xx_it.c  - 中断处理函数
└── Inc/
    ├── main.h          - 引脚宏定义
    ├── gpio.h          - GPIO 相关声明
    └── tim.h           - TIM 相关声明

七、使用方法
-----------
1. 使用 Keil uVision 打开 MDK-ARM/TIM_EXTI_test.uvprojx
2. 编译项目
3. 通过 ST-Link 或其他调试器烧录到 STM32F103C8T6
4. 观察现象:
   - PC13 LED: 每秒闪烁一次
   - PA0 LED: 平滑呼吸灯效果

八、注意事项
-----------
1. 确保外部晶振 (HSE) 正确连接 (通常为 8MHz)
2. PA0 连接 LED 时需要串联限流电阻 (通常 220Ω~1kΩ)
3. 如果 LED 不亮或呼吸灯不明显，检查:
   - 限流电阻是否过大
   - LED 极性是否正确
   - PA0 引脚是否与其他外设冲突

九、扩展功能
-----------
可以在此基础上添加:
1. EXTI 外部中断 (如按键触发)
2. 多通道 PWM 输出
3. PWM 频率/占空比动态调节
4. FreeRTOS 任务调度

十、实验问题与解决方案
-------------------

【实验一】TIM_test1 - LED_PC13 不亮
=====================================

问题描述：
烧录程序后，PC13 引脚的 LED 灯完全不亮。

问题分析：
在 main.c 中已经配置了 TIM2 定时器中断并启动了定时器：
    HAL_TIM_Base_Start_IT(&htim2);
并编写了回调函数 HAL_TIM_PeriodElapsedCallback() 来翻转 LED。

但是，缺少以下关键代码：
1. stm32f1xx_it.c 中没有 TIM2_IRQHandler() 中断处理函数
2. tim.c 的 HAL_TIM_Base_MspInit() 中没有配置 NVIC 中断优先级
3. stm32f1xx_it.c 中没有声明 extern TIM_HandleTypeDef htim2;

原因解释：
HAL_TIM_Base_Start_IT() 只是开启了定时器硬件的中断产生功能，
但 NVIC（嵌套向量中断控制器）还需要单独配置，告诉 CPU：
"当 TIM2 产生中断时，你要响应它"。

解决方案：
在 stm32f1xx_it.c 中添加：
1. 外部变量声明：
   extern TIM_HandleTypeDef htim2;

2. TIM2 中断处理函数：
   void TIM2_IRQHandler(void)
   {
     HAL_TIM_IRQHandler(&htim2);
   }

在 tim.c 的 HAL_TIM_Base_MspInit() 中添加：
    HAL_NVIC_SetPriority(TIM2_IRQn, 0, 0);
    HAL_NVIC_EnableIRQ(TIM2_IRQn);

经验教训：
使用 STM32CubeMX 生成代码时，需要在 TIM2 配置界面的
"NVIC Settings" 选项卡中勾选 "TIM2 global interrupt" 的
Enabled 选项，CubeMX 才会自动生成完整的中断支持代码。

---

【实验二】TIM_EXTI_test - PA0 呼吸灯快闪而不是慢闪
===================================================

问题描述：
PA0 引脚的外部 LED 没有呈现平滑的呼吸灯效果，
而是快速闪烁（亮灭交替）。

问题分析：
初始配置的 TIM2 参数：
    Prescaler = 7200-1 = 7199
    Period = 10000-1 = 9999

计算 PWM 频率：
    TIM2 时钟 = 72MHz
    计数器频率 = 72MHz / 7200 = 10kHz
    PWM 周期 = 10000 / 10kHz = 1 秒
    PWM 频率 = 1Hz

人眼视觉暂留效应：
人眼无法区分 50Hz 以下的亮度变化。
PWM 频率只有 1Hz，意味着每秒只能变化 1 次，
所以看到的是"快闪"而不是平滑渐变。

解决方案：
提高 PWM 频率到 10kHz 以上（人眼感知阈值），同时保持呼吸灯周期。

修改 tim.c 中的 TIM2 参数：
    Prescaler = 72-1 = 71
    Period = 100-1 = 99

新的 PWM 参数：
    PWM 频率 = 72MHz / 72 / 100 = 10kHz (> 50Hz，满足要求)
    PWM 分辨率 = 100 级 (0~99)

修改 main.c 中的呼吸灯逻辑：
    - pwm_val 上限从 1000 改为 100
    - 步进值保持 10
    - HAL_Delay(5) 保持不变

这样 PWM 输出 10kHz 的高频信号，
占空比在 0%~100% 之间每 5ms 变化 1%，完成一个呼吸灯周期约 1 秒。

经验教训：
PWM 用于 LED 调光时，必须确保 PWM 频率 > 50Hz，
否则人眼会感知到闪烁而不是平滑的亮度变化。

十一、版本信息
-----------
- 创建日期: 2026-05-30
- STM32 HAL 库版本: STM32F1xx_HAL_Driver
- 开发环境: Keil uVision 5
