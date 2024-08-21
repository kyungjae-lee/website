[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Real-Time Operating Systems (RTOS)</a> > FreeRTOS Task Notification

# FreeRTOS Task Notification



## FreeRTOS Task Notification

* Each RTOS task has a **32-bit notification value** (`ulNotifiedValue`) which is initialized to 0 when the RTOS task is created.

  ```c
  /* Project/Common/ThirdParty/FreeRTOS/task. c */
  
  typedef struct tskTaskControlBlock       /* The old naming convention is used to prevent breaking kernel aware debuggers. */
  {
  	...
      #if ( configUSE_TASK_NOTIFICATIONS == 1 )
      	volatile uint32_t ulNotifiedValue[ configTASK_NOTIFICATION_ARRAY_ENTRIES ];
      	volatile uint8_t ucNotifyState[ configTASK_NOTIFICATION_ARRAY_ENTRIES ];
      #endi
  } tskTCB;
  ```

* An RTOS task notification is an event sent directly to a task that can unblock the receiving task, and optionally update the receving task's notification value in a number of different ways. 

  e.g., A notification may overwrite the receiving task's notification value, or just set one or more bits in the receiving task's notification value.



## Some Notification Related APIs

### 1. `xTaskNotifyWait()`

* If a task calls `xTaskNotifyWait()`, then it waits (in Blocked state) with an optional timeout until it receives a notification from some other task or interrupt handler.

* Prototype

  ```c
  /* task.h */
  BaseType_t xTaskNotifyWait(uint32_t ulBitsToClearOnEntry,
                             uint32_t ulBitsToClearOnExit,
                             uint32_t *pulNotificationValue,
                             TickType_t xTicksToWait);
  ```

  > * `ulBitsToClearOnEntry`
  >
  >   Any bits set in `ulBitsToClearOnEntry` will be cleared in the calling RTOS task's notification value on entry to the `xTaskNotifyWait()` function (before the task waits for a new notification) provided a notification is not already pending when `xTaskNotifyWait()` is called. For example, if `ulBitsToClearOnEntry` is 0x01, then bit 0 of the task's notification value will be cleared on entry to the function. Setting `ulBitsToClearOnEntry` to 0xffffffff (ULONG_MAX) will clear all the bits in the task's notification value, effectively clearing the value to 0.
  >
  >   > `xTaskNotifyWait()` entry 순간에 clear 할 bits.
  >
  > * `ulBitsToClearOnExit`
  >
  >   Any bits set in ulBitsToClearOnExit will be cleared in the calling RTOS task's notification value before `xTaskNotifyWait()` function exits if a notification was received. The bits are cleared after the RTOS task's notification value has been saved in `*pulNotificationValue` (see the description of `pulNotificationValue` below). For example, if `ulBitsToClearOnExit` is 0x03, then bit 0 and bit 1 of the task's notification value will be cleared before the function exits. Setting `ulBitsToClearOnExit` to 0xffffffff (ULONG_MAX) will clear all the bits in the task's notification value, effectively clearing the value to 0.
  >
  >   > `xTaskNotifyWait()` notification 받고 exit 순간에 clear 할 bits.
  >
  > * `pulNotificationValue`
  >
  >   Used to pass out the RTOS task's notification value. The value copied to `*pulNotificationValue` is the RTOS task's notification value as it was before any bits were cleared due to the `ulBitsToClearOnExit` setting. If the notification value is not required then set `pulNotificationValue` to NULL.
  >
  > * `xTicksToWait`
  >
  >   The maximum time to wait in the Blocked state for a notification to be received if a notification is not already pending when `xTaskNotifyWait()` is called. (Basically, how many ticks it has to wait in the Blocked queue until the timeout.) The RTOS task does not consume any CPU time when it is in the Blocked state. The time is specified in RTOS tick periods. The `pdMS_TO_TICKS()` macro can be used to convert a time specified in milliseconds into a time specified in **ticks**.
  >
  > [!] Note: Notice that FreeRTOS APIs always take **ticks** as an argument instead of **milliseconds**.

* Return values

  * `pdTRUE` if a notification was received, or a notification was already pending when `xTaskNotifyWait()` was called.
  * `pdFALSE` if the call to `xTaskNotifyWait()` timed out before a notification was received. 

### 2. `xTaskNotify()`

* `xTaskNotify()` is used to send an event directly to and potentially unblock an RTOS task, and optionally update one of the receiving task’s notification values in one of the following ways:
  * Write a 32-bit number to the notification value
  * Add one (increment) the notification value
    * e.g., To keep track of how many times a user has pressed the button.
  * Set one or more bits in the notification value
  * Leave the notification value unchanged
* This function MUST NOT be called from an interrupt service routine (ISR).  Use [xTaskNotifyFromISR()](https://www.freertos.org/xTaskNotifyFromISR.html) instead.

* Prototype

  ```c
  /* task.h */
  BaseType_t xTaskNotify(TaskHandle_t xTaskToNotify,
                         uint32_t ulValue,
                         eNotifyAction eAction);
  ```

  >* `xTaskToNotify`
  >
  >  The handle of the RTOS task being notified. This is the *target* task. To obtain a task's handle create the task using  [xTaskCreate()](https://www.freertos.org/a00125.html) and make use of the pxCreatedTask parameter, or create the task using [xTaskCreateStatic()](https://www.freertos.org/xTaskCreateStatic.html) and store the returned value, or use the task's name in a call to [xTaskGetHandle()](https://www.freertos.org/a00021.html#xTaskGetHandle). The handle of the currently executing RTOS task is returned by the [xTaskGetCurrentTaskHandle()](https://www.freertos.org/a00021.html#xTaskGetCurrentTaskHandle) API function.
  >
  >* `ulValue`
  >
  >  Used to update the notification value of the target task. See the description of the eAction parameter below.
  >
  >* `eAction`
  >
  >  An enumerated type that can take one of the values documented in the table below in order to perform the associated action. (`eNoAction`, `eSetBits`, `eIncrement`, `eSetValueWithOverwrite`, `eSetValueWithoutOverwrite`)



## Examples

* `xTaskNotifyWait()` - [https://www.freertos.org/xTaskNotifyWait.html](https://www.freertos.org/xTaskNotifyWait.html)
* `xTaskNotify` - [https://www.freertos.org/xTaskNotify.html](https://www.freertos.org/xTaskNotify.html)





## References

Nayak, K. (2022). *Mastering RTOS: Hands on FreeRTOS and STM32Fx with Debugging* [Video file]. Retrieved from https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/

