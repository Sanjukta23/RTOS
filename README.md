# FreeRTOS on STM32F4

A progression of FreeRTOS exercises on the STM32F4, moving from basic task creation up through inter-task communication and synchronization primitives, instrumented with SEGGER SystemView for real-time task tracing over UART.

Each numbered folder is a standalone STM32CubeIDE project built on STM32 HAL + FreeRTOS, isolating one RTOS concept at a time.

## Topics covered

| Folder | Concept |
|---|---|
| `001Tasks` | Creating tasks with `xTaskCreate` and starting the scheduler with `vTaskStartScheduler` |
| `002LED_Tasks` | Multiple tasks independently driving LED output |
| `003LED_BlockingAPI_Task` | Blocking APIs (`vTaskDelay`) instead of busy-waiting inside a task |
| `004LED_Periodic_Tasks` | Periodic task execution patterns |
| `005LED_Task_Notify` | Lightweight task-to-task signaling via task notifications (`xTaskNotify` / `xTaskNotifyFromISR`) instead of a full semaphore |
| `006LED_Btn_ISR` | Deferring work from a GPIO interrupt (button press) to a task |
| `007Task_Priority` | Task priority levels and their effect on scheduling |
| `008Queues` | A queue-driven serial command interface: a UART RX interrupt feeds a byte queue, a command-handling task parses it, and a menu/print/LED/RTC task set responds — combined with software timers (`xTimerCreate`) for LED effects and periodic RTC reporting |
| `009Binary_Semaphore` | Binary semaphores for signaling between an ISR and a task |
| `010Counting_Semaphore` | Counting semaphores for tracking multiple available resources/events |
| `011Mutex_Semaphore` | Mutex-based mutual exclusion using a semaphore |
| `012Mutex` | Direct mutex API usage for protecting shared resources |
| `common/ThirdParty` | Shared third-party sources (FreeRTOS kernel and SEGGER SystemView) used across all the projects above |

### Highlight: `008Queues` — a queue-driven command shell

This is the most complete project in the repository. It runs five concurrent tasks (`menu_task`, `cmd_handler_task`, `print_task`, `led_task`, `rtc_task`) coordinated through:

- A byte queue (`q_data`) filled from a UART receive interrupt (`HAL_UART_RxCpltCallback`), with newline-triggered task notification to the command handler
- A second queue (`q_print`) for serializing output back to the terminal
- Four software timers driving independent LED blink effects, addressed by timer ID
- A software timer polling and reporting the RTC date/time over UART

It's a working example of a menu-driven embedded shell built entirely on FreeRTOS primitives (queues, notifications, and timers) rather than a monolithic superloop.

## Instrumentation

Most projects initialize **SEGGER SystemView** (`SEGGER_SYSVIEW_Conf`) and a UART-based SystemView transport (`SEGGER_UART_init`), enabling real-time visualization of task execution, context switches, and timing directly from SEGGER's SystemView desktop application.

## Hardware

- STM32F4-series development board (Discovery/Nucleo)
- Onboard user LED and push button for the LED/ISR exercises
- USART2 for serial I/O (SystemView transport and the `008Queues` command shell), 115200 baud

## Toolchain

- STM32CubeIDE with STM32CubeMX-generated peripheral initialization
- STM32 HAL drivers
- FreeRTOS kernel (bundled under `common/ThirdParty`)
- SEGGER SystemView for real-time RTOS trace visualization
- ST-Link for flashing and debugging

## Building and running

1. Open STM32CubeIDE and import the desired numbered folder as an existing project.
2. Build the project (`Project > Build`).
3. Flash to the target board via the onboard ST-Link.
4. Connect a serial terminal (or SEGGER SystemView) to the board's USART2 at 115200 baud to observe task output/tracing.

## Status

Personal learning repository, progressing from single-task scheduling basics to a multi-task, queue-and-timer-driven command interface.
