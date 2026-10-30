# 电控组第二次培训作业
- **ChenchangYu**
- ***20263001691***

## 流水灯实现思路
用 STM32CubeMX 配置了三个 GPIO 输出引脚，并将其分别命名为 LED0、LED1、LED2，代码中直接用宏定义控制，。

### 非阻塞延时原理
用了基于 HAL_GetTick()的系统滴答定时器来实现非阻塞延时，记录上一次切换的时间戳，并在主循环中不断计算当前时间与上一次的时间差，当时间差大于等于 500ms 时，触发状态机切换点亮的 LED，同时更新时间戳。这种方式让 CPU 在等待的 500ms 中可以执行另外的任务。

核心代码片段：

/* USER CODE BEGIN WHILE */
  uint32_t last_tick = 0;
  uint8_t  led_state = 0;
  while (1)
  {
    uint32_t now = HAL_GetTick();

    if (now - last_tick >= 500)
    {
      last_tick = now;

      HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_RESET);
      HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_RESET);
      HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_RESET);

      switch (led_state)
      {
        case 0:
          HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_SET);
          break;
        case 1:
          HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_SET);
          break;
        case 2:
          HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_SET);
          break;
        default:
          break;
      }

      led_state = (led_state + 1) % 3;
    }
    /* USER CODE END WHILE */
