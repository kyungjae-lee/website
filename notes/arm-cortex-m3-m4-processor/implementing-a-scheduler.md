[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">ARM Cortex-M3/M4 Processor</a> > Implementing a Scheduler

# Implementing a Scheduler



## Introduction

* Implement a scheduler which schedules multiple user tasks (we will use 4 tasks) in a Round-Robin fashion by carrying out the context switch operation. (Round-Robin scheduling algorithm - Time slices are assigned to each task in equal portions and in circular order.)
* Use SysTick handler to carry out the context switch operation between multiple tasks (Later update the code using PendSV for context switching)
* What is a task?
  * A task is nothing but a piece of code, or you can call it a "C function", which does a specific job when it is allowed to run on the CPU.
  * **A task has its own stack** to create its local variables when it runs on the CPU. Also, when the scheduler decides to remove a task from CPU, scheduler first saves the context (state) of the task in task's private stack.
  * In summary, a piece of code or a function is called a task when it is schedulable and never loses its 'state' unless it is deleted permanently.



## Implementation Guide

* **Create 4 user tasks** (i.e., 4 never returning C functions)

* **Stack point selection**

  * MSP - Handler mode (Scheduler; `SysTick_Handler()` / `PendSV_Handler()` )
  * PSP - Thread mode (User tasks)

  Our program will use both MSP and PSP for stack utilization.

* **Stack assessment**

  STM32F407 microcontroller board has 128 KB (SRAM1 + SRAM2) of RAM.

  We will use 5 KB of stack memory, each 1 KB of which will be assigned to each task and a scheduler. (4 tasks + 1 scheduler).

  How much stack to assign is completely dependent on your application design, the number of APIs to be called from the stack and so on.

  ```plain
  RAM_END     +-------------------+ T1_STACK_START
              | Private stack T1  |
              +-------------------+ T1_STACK_END = T2_STACK_START
              | Private stack T2  |
              +-------------------+ T2_STACK_END             
              | Private stack T3  |
              +-------------------+ 
              | Private stack T4  |
              +-------------------+ 
              |     Scheduler     |
              +-------------------+
              |                   |
              |        ...        |
              |                   |
  RAM_START   +-------------------+
  
  ```

* **Reserve stack areas for all the tasks and scheduler**

* **Scheduling policy selection**

  * Round-Robin preemptive scheduling
  * No task priority
  * Use SysTick timer to generate exception for every 1 ms to run the scheduler code

  > Scheduling
  >
  > * An algorithm which makes the decision of preempting a running task from the CPU and makes the decision about which task to dispatch (i.e., allocate CPU) next
  > * The decision could be based on many factors such as system load, the priority of tasks, share resource access, or a simple Round-Robin method.
  >
  > Context switching
  >
  > * The procedure of switching out the currently running task from the CPU after saving the task's execution context or state and switching in the next task's to run on the CPU by retrieving the past execution context or state of the task.
  >
  > State of a task
  >
  > * Inside a **microcontroller** (e.g., STM32x) is a **processor** (e.g., ARM Cortex-M4) which contains NVIC, MPU, SCP, FPU, Debug Unit, etc.
  >
  >   Inside a processor is a **processor core** which contains ALU, Core registers (General purpose, special purpose, special registers, etc.), etc. 
  >
  > * When a task is running, there will be various resources associated with it that contains the information about the task. In general, the state of a process = General purpose registers + Some special purpose registers + Status register. These are what need to be stored and retrieved during the context switching.
  >
  >   * General purpose registers
  >
  >   * PC - Will be holding the next instruction the preempted task needs to execute when it is dispatched again.
  >
  >   * LR - Return address
  >
  >   * PSP - Stack pointer for the user tasks (contains information about how each task is using the stack)
  >
  >   * PSR - Snapshot of the status flags (N, V, Z, C, etc.)
  >
  >   * Other special registers such as PRIMASK, FAULTMASK, BASEPRI, CONTROL registers are privileged registers and they will NOT be stored! (Most of the time user tasks will be running with unprivileged access level so storing these registers wouldn't make any sense. Kernel should touch these registers not the user tasks.)
  >
  > 
  >
  >   <img src="./img/state-of-a-user-task.png" alt="state-of-a-user-task" width="700">
  >
  > 
  >
  > * Case study of context switching: T1 switching out, T2 switching in
  >
  >   1. Running T1
  >   
  >   2. Scheduler (Context Switching)
  >   
  >      1. Context saving 
  >   
  >         * `push` the context of T1 onto T1's private stack
  >   
  >         * Save the PSP value of T1 using a global variable
  >   
  >      2. Context retrieving 
  >   
  >         * Retrieve the PSP value of T2 from the corresponding global variable
  >   
  >         * `pop` the context of T2 from T2's private stack
  >   
  >   3. Run T2
  >   
  > * As an exception entry sequence, the following registers will get pushed onto stack automatically by the processor:
  >
  >   * R0, R1, R2, R3, R12, LR, PC, xPSR (we'll call it Stack Frame 1 or SF1)
  >
  >   So, our job in implementing scheduler is to make sure that the rest of the task state will get pushed onto stack as well.
  >
  >   * R4 - R11 (we'll call it Stack Frame 2 or SF2)
  >
  > * Task's stack area initialization and storing of dummy stack frame
  >
  >   * Each task can consume a maximum of 1 KB of memory as a private stack.
  >
  >   * This stack is used to hold tasks' local variables and context (SF1 + SF2)
  >
  >   * When a task is getting scheduled for the very first time, it doesn't have any context. So, it is the programmer's responsibility to store dummy SF1 and SF2 in the task's stack area as a part of "Task initialization" sequence before launching the scheduler.
  >
  >     * Set all the general purpose registers to 0 (R0 - R12)
  >
  >     * PSR - 0x01000000
  >
  >       Only the T bit is important (bit[24]) and it must be set (1) for ARM Cortex-M4
  >
  >     * PC - Address of the `task2_handler()` make sure that lsb of the address is 1; for T bit.
  >
  >     * LR - A special value EXC_RETURN which controls the exception exit.
  >
  >       0xFFFFFFFD is appropriate for our design. (Return to Thread mode, exception return uses non-floating-point state from the PSP and execution uses PSP after return.)
  
* **Configure the SysTick timer to produce exception every 1 ms.**

  * Processor Clock = 16 MHz

  * SysTick timer count clock = 16 MHz

  * 1 ms is 1 KHz in frequency domain (in 1 sec, a thousand context switches will happen)

  * So, to bring down SysTick timer count clock from 16 MHz to 1 KHz use a divisor (i.e., reload value)

    1 KH is the TICK_HZ (desired exception frequency)

    $\therefore$ Reload value = 16000 (In fact, 15999 is the reload value. Keep reading!)
    
  * You'll need to use "SysTick Reload Value Register (`SYST_RVR`)" and `SYST_CVR`. The initial reload value stored in `SYST_RVR` gets copied into `SYST_CVR` and this is the count start value. When the down count of `SYST_RVR` reaches 0, the next count value is retrieved from `SYS_CVR` so this count start value must not be modified!
  
  * Also, since the exception gets triggered when the reload value is reset to the start value after reaching 0, you must use N-1 for the actual reload value where N is the number of clock cycle required for every exception. So, if we want the exception to occur every 16000 clock cycle, the reload value should be 15999. 


* **Initialize scheduler stack pointer (MSP)**
  * To secure MSP we need, naked function will be used for initialization.
  
* **Initialize tasks' stack memory**
  * Store dummy SF1 and SF2

* (After the first attempt to control 4 LEDs with x4 elongation) **Introducing blocking state for tasks**.
  * When a task has got nothing to do, it should simply call a delay function which should put the task into the BLOCKED state from READY state until the specified delay is elapsed.
  
  * We should now maintain 2 states for a task; READY an BLOCKED.
  
  * The scheduler should schedule ONLY those tasks which are in READY state.
  
  * The scheduler also should unblock the blocked tasks if their blocking period is over and put them back to READY state.
  
  * Update the code by introducing the Task Control Block (TCB). TCB will replace a number of global variables you declared before.
  
    ```c
    typedef struct
    {
    	uint32_t psp_value;
    	uint32_t block_count;
    	uint8_t current_state;
    	(void)(*task_handler)(void);
    } TCB_t;
    ```
  
* **Block a task for a given number of ticks**


  * Introduce a function called `task_delay()` which puts the calling task to the BLOCKED state for a given number of ticks. `task_delay()` is not a software-based (i.e., using `for` loop) delay.

    e.g., If a task calls `task_delay(1000)` then the function puts the task into BLOCKED state and allows the next task to run on the CPU.

    Here, the number 1000 denotes a block period in terms of ticks, the task who calls this function is going to block for 1000 ticks (SysTick exceptions), i.e., for 1000 ms since each tick happens for every 1 ms.

  * The scheduler should check elapsed block period of each blocked task and put them back to READY state if the block period is over.

  * The `task_delay()` function will be implemented by using a global tick count.

* **Idle task**

  What if all the tasks are blocked? Who is going to run on the CPU?

  * Use the idle task to run on the CPU if all the tasks are blocked. The idle task is like user tasks but only runs when all user tasks are blocked, and you can put the CPU to sleep.

* **Global tick count**


  * How does the scheduler decide when to put the BLOCKED state tasks (blocked using `task_delay()` function) back to the READY state?

    In a real-time operating system, or a complete embedded OS, a task can block for a number of reasons. e.g., To wait for an event, by calling the `delay()` function, to block over a semaphore, etc.

    In our example, though, a task can block only when it calls the `task_delay()` function.

  * It has to compare the task's **delay tick count** with a global tick count.

  * So, scheduler should maintain a global tick count and update it for every SysTick exception.

* **Revisit deciding which task to run next**

  * With the two states of tasks introduced, now it depends on the "state" of the next task.
    * If next task is in BLOCKED state, skip it and check the one comes next until there is a task in READY state that is not the Idle task. 
    * If all other tasks are found to be in the BLOCKED state, then select the Idle task.
  * State of the Idle task will always be READY, and it cannot be changed.

* **Now, time to introduce PendSV handler**

  * PendSV is a better choice for handling context switches than SysTick.

  * Implement `SysTick_Handler()` as a regular C function, `PendSV_Handler()` as a naked function.

    * `SysTick_Handler()`

      Update global tick count $\to$ Unblock task (if there is any blocked task that is qualified to become READY state) $\to$ Pend PendSV $\to$ Exit

    * `PendSV_Handler()`

      Carry out context switch $\to$ Exit

  * To change PendSV exception state to pending:

    **Interrupt Control and State Register (ICSR)** $\to$ Set bit[28] PENDSVSET



## Implementation

* **led.h**

  ```c
  /*
   * led.h
   *
   *  Created on: Feb 10, 2023
   *      Author: klee
   */
  
  #include <stdint.h>
  
  #ifndef LED_H_
  #define LED_H_
  
  #define LED_GREEN  12
  #define LED_ORANGE 13
  #define LED_RED    14
  #define LED_BLUE   15
  
  #define DELAY_COUNT_1MS 		 1250U
  #define DELAY_COUNT_1S  		(1000U * DELAY_COUNT_1MS)
  #define DELAY_COUNT_500MS  		(500U  * DELAY_COUNT_1MS)
  #define DELAY_COUNT_250MS 		(250U  * DELAY_COUNT_1MS)
  #define DELAY_COUNT_125MS 		(125U  * DELAY_COUNT_1MS)
  
  void led_init_all(void);
  void led_on(uint8_t led_no);
  void led_off(uint8_t led_no);
  void delay(uint32_t count);
  
  #endif /* LED_H_ */
  ```

* **led.c**

  ```c
  /*
   * led.c
   *
   *  Created on: Feb 10, 2023
   *      Author: klee
   */
  
  #include<stdint.h>
  #include "led.h"
  
  // Software based delay (this will introduce some problem in our example).
  // Timer-based delay is what can solve the issue.
  void delay(uint32_t count)
  {
    for(uint32_t i = 0 ; i < count ; i++);
  }
  
  void led_init_all(void)
  {
  
  	uint32_t *pRccAhb1enr = (uint32_t*)0x40023830;
  	uint32_t *pGpiodModeReg = (uint32_t*)0x40020C00;
  
  
  	*pRccAhb1enr |= ( 1 << 3);
  	//configure LED_GREEN
  	*pGpiodModeReg |= ( 1 << (2 * LED_GREEN));
  	*pGpiodModeReg |= ( 1 << (2 * LED_ORANGE));
  	*pGpiodModeReg |= ( 1 << (2 * LED_RED));
  	*pGpiodModeReg |= ( 1 << (2 * LED_BLUE));
  
  #if 0
  	//configure the outputtype
  	*pGpioOpTypeReg |= ( 1 << (2 * LED_GREEN));
  	*pGpioOpTypeReg |= ( 1 << (2 * LED_ORANGE));
  	*pGpioOpTypeReg |= ( 1 << (2 * LED_RED));
  	*pGpioOpTypeReg |= ( 1 << (2 * LED_BLUE));
  #endif
  
      led_off(LED_GREEN);
      led_off(LED_ORANGE);
      led_off(LED_RED);
      led_off(LED_BLUE);
  }
  
  void led_on(uint8_t led_no)
  {
    uint32_t *pGpiodDataReg = (uint32_t*)0x40020C14;
    *pGpiodDataReg |= ( 1 << led_no);
  
  }
  
  void led_off(uint8_t led_no)
  {
  	  uint32_t *pGpiodDataReg = (uint32_t*)0x40020C14;
  	  *pGpiodDataReg &= ~( 1 << led_no);
  
  }
  ```

* **main.h**

  ```c
  /*
   * main.h
   *
   *  Created on: Feb 10, 2023
   *      Author: klee
   */
  
  #ifndef MAIN_H_
  #define MAIN_H_
  
  #define MAX_TASKS 5
  #define DUMMY_XPSR 0x01000000U // guarantees T-bit set
  
  /* stack memory calculation */
  #define SIZE_TASK_STACK 1024U
  #define SIZE_SCHED_STACK 1024U
  
  #define SRAM_START 0x20000000U
  #define SIZE_SRAM ((128) * (1024))
  #define SRAM_END ((SRAM_START) + (SIZE_SRAM))
  
  #define T1_STACK_START SRAM_END
  #define T2_STACK_START (SRAM_END) - (1 * (SIZE_TASK_STACK))
  #define T3_STACK_START (SRAM_END) - (2 * (SIZE_TASK_STACK))
  #define T4_STACK_START (SRAM_END) - (3 * (SIZE_TASK_STACK))
  #define IDLE_STACK_START (SRAM_END) - (4 * (SIZE_TASK_STACK))
  #define SCHED_STACK_START (SRAM_END) - (5 * (SIZE_TASK_STACK))
  
  #define TICK_HZ 1000U
  #define HSI_CLOCK 16000000U
  #define SYSTICK_TIM_CLK	HSI_CLOCK
  
  // task states
  #define TASK_READY_STATE 0x00
  #define TASK_BLOCKED_STATE 0xFF
  
  #define INTERRUPT_DISABLE() do {__asm volatile("mov r0, #0x1"); __asm volatile("msr PRIMASK, r0");} while(0)
  	// good technique when you want to #define multiple statements
  #define INTERRUPT_ENABLE() do {__asm volatile("mov r0, #0x0"); __asm volatile("msr PRIMASK, r0");} while(0)
  
  #endif /* MAIN_H_ */
  ```

* **main.c**

  ```c
  /**
   ******************************************************************************
   * @file           : main.c
   * @author         : Auto-generated by STM32CubeIDE
   * @brief          : Main program body
   ******************************************************************************
   * @attention
   *
   * Copyright (c) 2023 STMicroelectronics.
   * All rights reserved.
   *
   * This software is licensed under terms that can be found in the LICENSE file
   * in the root directory of this software component.
   * If no LICENSE file comes with this software, it is provided AS-IS.
   *
   ******************************************************************************
   */
  
  #include <stdint.h>
  #include <stdio.h>
  #include "main.h"
  #include "led.h"
  
  #if !defined(__SOFT_FP__) && defined(__ARM_FP)
    #warning "FPU is not initialized, but the project is compiling for an FPU. Please initialize the FPU before use."
  #endif
  
  void task1_handler(void); // user task 1 of the application
  void task2_handler(void); // user task 2
  void task3_handler(void); // user task 3
  void task4_handler(void); // user task 4
  void init_systick_timer(uint32_t tick_hz);
  __attribute__((naked)) void init_scheduler_stack(uint32_t sched_top_of_stack);
  void init_tasks_stack(void);
  __attribute__((naked)) void switch_sp_to_psp(void);
  void enable_processor_faults(void);
  
  // fault handlers
  void HardFault_Handler(void);
  void MemManage_Handler(void);
  void BusFault_Handler(void);
  
  void task_delay(uint32_t tick_count); // tick_count: for how many ticks the task should
  									  // remain in BLOCKED state
  void idle_task(void);
  
  uint8_t current_task = 1; // task 0 is idle task, start with task1
  	// idle task should be launched only when all the tasks are blocked
  uint32_t g_tick_count = 0;
  
  typedef struct
  {
  	uint32_t psp_value;
  	uint32_t block_count;
  	uint8_t current_state;
  	void (*task_handler)(void);
  } TCB_t;
  
  TCB_t user_tasks[MAX_TASKS];
  
  int main(void)
  {
  	// enable processor faults so we can track invalid access to memory if any
  	enable_processor_faults();
  
  
  	// initialize scheduler stack
  	init_scheduler_stack(SCHED_STACK_START);
  
  	// initialize tasks's stack with dummy stack frame
  	init_tasks_stack(); // all tasks will be initialized inside this function
  
  	// initialize all the LEDs
  	led_init_all();
  
  
  	// generate the SysTick timer exception
  	init_systick_timer(TICK_HZ);
  
  	// Launch the first task. (Needs to run with the MSP stack pointer)
  	switch_sp_to_psp();
  	task1_handler();
  
      /* Loop forever */
  	for(;;);
  }
  
  void idle_task(void)
  {
  	while (1);
  }
  
  void task1_handler(void)
  {
  	while (1)
  	{
  		led_on(LED_GREEN);
  		task_delay(1000); // will be blocked for the next 1000 ticks
  		led_off(LED_GREEN);
  		task_delay(1000); // will be blocked for the next 1000 ticks
  	}
  }
  
  void task2_handler(void)
  {
  	while (1)
  	{
  		led_on(LED_ORANGE);
  		task_delay(500); // will be blocked for the next 1000 ticks
  		led_off(LED_ORANGE);
  		task_delay(500); // will be blocked for the next 1000 ticks
  	}
  }
  
  void task3_handler(void)
  {
  	while (1)
  	{
  		led_on(LED_BLUE);
  		task_delay(250); // will be blocked for the next 1000 ticks
  		led_off(LED_BLUE);
  		task_delay(250); // will be blocked for the next 1000 ticks
  	}
  }
  
  void task4_handler(void)
  {
  	while (1)
  	{
  		led_on(LED_RED);
  		task_delay(125); // will be blocked for the next 1000 ticks
  		led_off(LED_RED);
  		task_delay(125); // will be blocked for the next 1000 ticks
  	}
  }
  
  void init_systick_timer(uint32_t tick_hz)
  {
  	// SysTick Reload Value Register: 0xE000E014
  	// Do not modify CVR (Current Value Register), SVR is the one to modify.
  	// Every clock cycle the CVR value will be decremented by 1,
  	// and when CVR value reaches 0, it will first reload the SVR value
  	// to itself and then the exception will be called. (Note that the exception
  	// is not called as soon as the CVR value reaches 0.)
  	// This is why you need to use a RELOAD value of N-1.
  	// -> Search SYST_RVR in the processor generic user guide.
  	uint32_t *pSRVR = (uint32_t *)0xE000E014;
  	uint32_t *pSCSR = (uint32_t *)0xE000E010;
  	uint32_t count_value = (SYSTICK_TIM_CLK / tick_hz) - 1;
  		// Tf the SysTick interrupt is required every 100 clock pulses, set RELOAD to 99.
  
  	// load the value into SVR
  	*pSRVR &= ~(0x00FFFFFF); // only 24 bits are valid in this register (don't touch reserved bits)
  	*pSRVR |= count_value;
  
  	// Do some settings on SysTick Control and Status Register (SYST_CSR): 0xE000E010
  	// Bit[2] CLKSOURCE: set (since SysTick timer will pull and use processor clock)
  	// Bit[1] TICKINT: set (counting down to zero asserts the SysTick exception request)
  	*pSCSR |= (1 << 1);
  	*pSCSR |= (1 << 2);
  	// Bit[0] ENABLE: set (counter enabled)
  	*pSCSR |= (1 << 0);
  }
  
  __attribute__((naked)) void init_scheduler_stack(uint32_t sched_top_of_stack)
  {
  	//__asm volatile("msr, msp, r0"); // since r0 will contain the value (AAPCS)
  	__asm volatile("msr msp, %0": : "r"(sched_top_of_stack):);
  	__asm volatile("bx lr"); // bx(branch indirect) does [pc] <- [lr] (return from function call)
  }
  
  void init_tasks_stack(void)
  {
  	user_tasks[0].current_state = TASK_READY_STATE; // idle task - always running
  	user_tasks[1].current_state = TASK_READY_STATE;
  	user_tasks[2].current_state = TASK_READY_STATE;
  	user_tasks[3].current_state = TASK_READY_STATE;
  	user_tasks[4].current_state = TASK_READY_STATE;
  
  	user_tasks[0].psp_value = IDLE_STACK_START;
  	user_tasks[1].psp_value = T1_STACK_START;
  	user_tasks[2].psp_value = T2_STACK_START;
  	user_tasks[3].psp_value = T3_STACK_START;
  	user_tasks[4].psp_value = T4_STACK_START;
  
  	user_tasks[0].task_handler = idle_task;
  	user_tasks[1].task_handler = task1_handler;
  	user_tasks[2].task_handler = task2_handler;
  	user_tasks[3].task_handler = task3_handler;
  	user_tasks[4].task_handler = task4_handler;
  
  	uint32_t *pPSP;
  
  	for (int i = 0; i < MAX_TASKS; i++)
  	{
  		pPSP = (uint32_t *)user_tasks[i].psp_value;
  
  		// ARM Cortex-M4 stack model is Full Descending:
  		// Decrement first, and then store the value
  		pPSP--;
  		*pPSP = DUMMY_XPSR; // 0x01000000
  
  		pPSP--; // pc
  		*pPSP = (uint32_t)user_tasks[i].task_handler; // all elements must be odd to maintain T bit
  
  		pPSP--; // lr
  		*pPSP = 0xFFFFFFFD; // EXC_RETURN
  
  		// rest of the stack frame (SF1, SF2) are to be initilized to 0
  		for (int j = 0; j < 13; j++)
  		{
  			pPSP--;
  			*pPSP = 0;
  		}
  
  		user_tasks[i].psp_value = (uint32_t)pPSP; // preserve PSPs
  	}
  }
  
  // Round-Robin
  void update_next_task(void)
  {
  	int state = TASK_BLOCKED_STATE; // why initialize to this state?
  	for (int i = 0; i < MAX_TASKS; i++)
  	{
  		current_task++;
  		current_task %= MAX_TASKS;
  		state = user_tasks[current_task].current_state;
  		// if current task is a schedulable task
  		if (state == TASK_READY_STATE && current_task != 0)
  			break;;
  	}
  	// at this point:
  	// 1. a schedulable task (other than idle task) is found
  	// 2. all tasks other than the idle task are BLOCKED
  
  	// if case 2
  	if (state == TASK_BLOCKED_STATE)
  		current_task = 0;
  }
  
  uint32_t get_psp_value(void)
  {
  	return user_tasks[current_task].psp_value;
  }
  
  
  void save_psp_value(uint32_t current_psp_value)
  {
  	user_tasks[current_task].psp_value = current_psp_value;
  }
  
  __attribute__((naked)) void switch_sp_to_psp(void)
  {
  	__asm volatile("push {lr}"); // preserve LR to later return back to main()
  
  	// 1. Initialize the PSP with TASK1 stack start
  	// get the value of psp of current task
  	__asm volatile("bl get_psp_value"); // bl since you need to come back
  		// according to AAPCS, initial SP of the current task will be stored in r0
  	__asm volatile("msr psp, r0");	// initialize PSP
  	__asm volatile("pop {lr}");
  
  	// up to this point MSP has been used
  
  	// 2. Change SP to PSP using CONTROL register
  	// Bit[1] SPSEL: set
  	__asm volatile("mov r0, #0x02");
  	__asm volatile("msr CONTROL, r0"); // wouldn't it corrupt other bits of CONTROL reg?
  
  	__asm volatile("bx lr"); // force return to main()
  }
  
  void enable_processor_faults(void)
  {
  	uint32_t *pSHCSR = (uint32_t *)0xE000ED24;
  
  	*pSHCSR |= (1 << 16); // MemManage
  	*pSHCSR |= (1 << 17); // BusFault
  	*pSHCSR |= (1 << 18); // UsageFault
  }
  
  void schedule(void)
  {
  	// Simply pend the PendSV exception (set bit[28] PENDSVSET)
  	uint32_t *pICSR = (uint32_t *)0xE000ED04;
  	*pICSR |= (1 << 28);
  }
  
  void task_delay(uint32_t tick_count)
  {
  	// Disable interrupt first to prevent 'race condition'. (Use PRIMASK register)
  	// PRIMASK: Priority Mask Register, if bit[0] is set, prevents the activation
  	// of all exceptions with configurable priority.
  	// Since user_task[] is a global array which can be accessed by both
  	// the handler mode code and the thread mode code.
  	INTERRUPT_DISABLE();
  		// All interrupts including SysTick exception, etc. are disabled.
  		// Only Thread mode code is running.
  
  	// if current_task is idle task, do nothing and return
  	if (current_task)
  	{
  		user_tasks[current_task].block_count = g_tick_count + tick_count;
  			// delay period is calculated with respect to g_tick_count
  			// in other words, when g_tickc_count becomes the block_count value in the future
  		    // this task will be qualified to be changed to RUNNING state
  		user_tasks[current_task].current_state = TASK_BLOCKED_STATE;
  
  		// allow other available task to run
  		schedule(); // trigger the PendSV handler
  	}
  
  	// Enable interrupt
  	INTERRUPT_ENABLE();
  }
  
  // does context switch
  __attribute__((naked)) void PendSV_Handler()
  {
  	// c.f. To debug an interrupt handler, you need to set a breakpoint inside it
  	// and run the program. Stepping-over line by line will not detect it.
  
  	// Save the context of current task. -------------------------------------------
  	// 1. Get current running task's PSP value
  	__asm volatile("mrs r0, psp");
  	// 2. Using that PSP value store SF2 (R4 - R11); SF1 is already store by the
  	//    processor.
  	//    Do not use push since in a handler always MSP will be used.
  	//    This is the case where you need store register contents into memory.
  	//    Use 'stmdb': store multiple registers, decrement before (This simulates
  	//    push operation of ARM Cortex-M4 whose stack model is Full Descending)
  	__asm volatile("stmdb r0!, {r4-r11}"); // if ! is appended, "write back", which
  		// means the final address, that is loaded from or stored to, is written back
  		// into r0 (update r0)
  
  	__asm volatile("push {lr}"); // preserve EXC_RETURN value
  		// this has to be done manually since it's a naked function
  
  	// 3. Save the current PSP value
  	__asm volatile("bl save_psp_value");
  
  	// Retrieve the context of next task -------------------------------------------
  	// 1. Decide next task to run
  	__asm volatile("bl update_next_task");
  	// 2. Get its past PSP value (At this point current_task value will have been
  	//    updated already.
  	__asm volatile("bl get_psp_value"); // r0 will contain PSP of the current_task
  	// 3. Using that PSP value retrieve SF2 (R4 to R11)
  	//    Do not use pop since in a handler always MSP will be used.
  	//    This is the case where you need load memory contents to registers.
  	//    Use 'ldmia': load multiple registers, increment after (ia is default)
  	//    pop operation of ARM Cortex-M4 whose stack model is Full Descending)
  	__asm volatile("ldmia r0!, {r4-r11}"); // if ! is appended, "write back", which
  		// means the final address, that is loaded from or stored to, is written back
  		// into r0 (update r0)
  	// 4. Update PSP and exit.
  	__asm volatile("msr psp, r0");
  		// now psp points to the stack of the task to be executed
  
  	__asm volatile("pop {lr}");
  	__asm volatile("bx lr"); // this has to be done manually since naked function
  	// At this point, context switch has been completed. SysTick handler exit sequence
  	// will take place automatically bye the processor.
  	// Q: Does PC now point to the first instruction the task to be executed should run?
  }
  
  void update_global_tick_count(void)
  {
  	g_tick_count++;
  }
  
  void unblock_tasks(void)
  {
  	for (int i = 1; i < MAX_TASKS; i++) // i = 0 -> idle task (no need to check)
  	{
  		if (user_tasks[i].current_state != TASK_READY_STATE)
  		{
  			if (user_tasks[i].block_count == g_tick_count)
  			{
  				user_tasks[i].current_state = TASK_READY_STATE;
  			}
  		}
  	}
  }
  
  // Scheduler (does not have to be 'naked' function)
  void SysTick_Handler(void)
  {
  	uint32_t *pICSR = (uint32_t *)0xE000ED04;
  	update_global_tick_count();
  
  	// Determine if the BLOCKED task meets condition to become RUNNING state.
  	unblock_tasks();
  
  	// Pend the PendSV exception (set bit[28] PENDSVSET)
  	*pICSR |= (1 << 28);
  }
  
  void HardFault_Handler(void)
  {
  	printf("Exception: HardFault\n");
  	while(1);
  }
  
  void MemManage_Handler(void)
  {
  	printf("Exception: MemManage\n");
  	while(1);
  }
  
  void BusFault_Handler(void)
  {
  	printf("Exception: BusFault\n");
  	while(1);
  }
  ```

  



## References

Nayak, K. (2022). *Embedded Systems Programming on ARM Cortex-M3/M4 Processor* [Video file]. Retrieved from  https://www.udemy.com/course/embedded-system-programming-on-arm-cortex-m3m4/
