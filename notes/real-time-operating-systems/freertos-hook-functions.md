[Home](../../) | [Projects](../../projects) | [Notes](../) >  <a href="./">Real-Time Operating Systems (RTOS)</a> > FreeRTOS Hook Functions

# FreeRTOS Hook Functions



## FreeRTOS Hook Functions

* FreeRTOS hook functions can be implemented according to the needs of the application. 

* The FreeRTOS kernel will call these hook functions whenever the corresponding events occur.

* FreeRTOS hook functions:

  * Idle Hook Function

  * Tick Hook Function

  * Malloc Failed Hook Function (Dynamic memory allocation fail)

  * Stack Overflow Hook Function

  * Daemon Task Startup Hook
* To use these hook functions, enable the corresponding configuration items in the `FreeRTOSConfig.h` file and implement them in the application.

### Idle Task Hook Function

* Idle task hook function implements a callback from idle task to your application.

* Idle hook function configuration must be enabled first before it can be used.

  ```c
  /* FreeRTOSConfig.h */
  #define configUSE_IDLE_HOOK 1
  ```

  Then, implement the following function in your application.

  ```c
  /* main.c */
  void vApplicationIdleHook(void) { /* code */ }
  ```

  In `main.c` ,`vTaskStartScheduler()` $\to$ `xTaskCreateStatic()` $\to$ `prvIdleTask` (task handle of the idle task), and you will see that `vApplicationIdleHook()` is getting called there.

* That's it! Whenever the idle task runs, the hook function will get called and you can utilize this function to do some useful things such as sending the MCU to the low-power mode to save power consumption. This allows the application designer to add background functionality without the overhead of a separate task.

* `vApplicationIdleHook()` must not, under any circumstances, call a function that might block!

### RTOS Tick Hook Function

* RTOS tick hook function is called whenever the SysTick exception is triggered.

* Tick hook function configuration must be enabled before it can be used.

  ```c
  /* FreeRTOSConfig.h */
  #define configUSE_TICK_HOOK 1
  ```

  Then, implement the following function in your application.

  ```c
  /* main.c */
  void vApplicationTickHook(void) { /* code */ }
  ```

  In `port.c`, `xPortSysTickHandler()` $\to$ `xTaskIncrementTick()` $\to$ `vApplicationTickHook()` is getting called here.

### Malloc Failed Hook Function

* When you use `malloc()` in your FreeRTOS application, and if there's no sufficient memory to allocate from the heap section of the RAM, then the allocation will fail and the kernel will notify you by calling this malloc failed hook function. 

* Malloc hook function configuration must be enabled before it can be used.

  ```c
  /* FreeRTOSConfig.h */
  #define configUSE_MALLOC_FAILED_HOOK 1
  ```

  > When this is enabled, the kernel will report to the programmer when `malloc` fail occurs by calling the following hook function.

  Then, implement the following function in your application.

  ```c
  /* main.c */
  void vApplicationMallocFailedHook(void) { /* code */ }
  ```

  Track down the `xTaskCreate()` function in `main.c` and see where this is getting called!

### Stack Overflow Hook Function

* If, during the run-time, more stack memory is consumed than a task is allocated, then the kernel will notify you by calling this stack overflow hook function.

* Stack overflow hook function configuration must be enabled before it can be used.

  ```c
  /* FreeRTOSConfig.h */
  #define configUSE_FOR_STACK_OVERFLOW 1
  ```

  Then, implement the following function in your application.

  ```c
  /* main.c */
  void vApplicationStackOverflowHook(TaskHandle_t xTask, signed char *pcTaskName) { /* code */ }
  ```

* When a task consumes more stack memory than it is allocated, this hook function will be called.

  For example,

  ```c
   status = xTaskCreate(task1_handler, "Task-1", 200, NULL, 2, &task1_handle);
  ```

  > Task-1's allowed stack size is 200 **words**. So, in this case, 800 bytes.





## References

Nayak, K. (2022). *Mastering RTOS: Hands on FreeRTOS and STM32Fx with Debugging* [Video file]. Retrieved from https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/

