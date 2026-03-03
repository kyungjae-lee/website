[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > HardFault

# HardFault

Debugging HardFault on ARM Cortex-M.



## What is a HardFault?

On the ARM Cortex-M architecture, a HardFault is a high-priority fault exception that represents either:

* An escalated configurable fault (MemManage, BusFault, UsageFault)
* A fault that occurred while those were disabled
* A vector table read failure
* A fault during fault handling (escalation)

Important:

> A HardFault is usually the *symptom*, not the root cause.



## Common Root Causes in Real Firmware

Especially in embedded systems like BMS, motor control, or RTOS-based systems:

* Stack overflow
* Invalid pointer dereference
* Calling through corrupted function pointer
* Accessing peripheral before clock enable
* Misaligned memory access
* Divide-by-zero (if trap enabled)
* Corrupted ISR vector
* Fault inside an interrupt with invalid priority configuration



## HardFault Debugging Procedure Overview

##### Step 1. Capture the Stack Frame

Using a naked handler to catch the stack frame of interest.

##### Step 2. Identify the Faulting `pc`

Check the value of `pc` and look it up in disassembly.

##### Step 3. Inspect SCB Fault Registers

Important registers:

* CFSR (Configurable Fault Status Register)
* HFSR (HardFault Status Register)
* MMFAR (Memory Manage Fault Address Register)
* BFAR (Bus Fault Address Register)

CFSR must be decoded bit-by-bit.



## HardFault Debugging Procedure in Detail

### Step 1. Capture the Stack Frame

To properly capture the stack frame of interest, which includes the `pc`, typically pointing to the the address of the instruction that would have executed next if no exception has occurred, define a naked function using the same name as the HardFault handler in the vector table, and rename the original C HardFault (`HardFault_Handler_C()`) to be called from the naked wrapper after extracting the stack pointer.

```c
/* NOTE: The following function name must be the same as the HardFault handler 
 * in the vector table. */
__attribute__((naked)) void HardFault_Handler(void)
{
    __asm volatile(
    	"tst lr, #4		\n"		/* To check which stack pointer was active. */
    	"ite eq			\n"		
        "mrseq r0, msp	\n"
        "mrsne r0, psp	\n"
        "b HardFault_Handler_C"
    )
}
```

> * `tst lr, #4` - Test bit 2 of the link register (EXC_RETURN) to determine which stack pointer was used. 0 = MSP (Main Stack Pointer), 1 = PSP (Process Stack Pointer).
> * `ite eq` - If-Then-Else instruction: the next two instructions will execute conditionally based on the Zero flag (Z) from the previous `tst`. `eq` branch executes if Z = 1 (`tst` result was 0), `ne` branch if Z = 0.
> * `mrseq r0, msp` - If Z = 1 (bit 2 of `lr` = 0), move `msp` into `r0`.
> * `mrsne r0, psp` - If Z = 0 (bit 2 of `lr` = 1), move `psp` into `r0`.
> * Branch to the C function that handles the HardFault, passing the stack pointer in `r0` by the ARM Procedure Call Standard (AAPCS).

### Step 2. Identify the Faulting PC

```c
void HardFault_Handler_C(uint32_t *sp)
{
    uint32_t r0 	= sp[0];
    uint32_t r1 	= sp[1];
    uint32_t r2 	= sp[2];
    uint32_t r3 	= sp[3];
    uint32_t r12 	= sp[4];
    uint32_t lr 	= sp[5];
    uint32_t pc 	= sp[6];	/* Important! */
    uint32_t psr 	= sp[7];
}
```

The key register here is the `pc`, as it points to the instruction that reveals where the HardFault likely occurred. Note that the `pc` may not always point exactly to the instruction that caused the HardFault or the next instruction to be executed. Once you have obtained the `pc`, you must examine the disassembly around the address to determine what might have caused the fault.

The stacked `pc` contains the address of the instruction that would have executed next if no exception has occurred. In practice:

* For faults caused by memory access, alignment, or usage errors: the stacked PC usually points to the faulting instruction itself.
* For faults during instruction prefetch: it may point to the next instruction, depending on the exact pipeline stage when the fault occurred.



## Why Use a Naked Function?

### Understanding Exception Entry (Critical Foundation)

When an exception occurs, the Cortex-M core, the hardware, automatically pushes the following registers onto the active stack (MSP or PSP):

```plain
r0		/* Argument/scratch */
r1		/* Argument/scratch */
r2		/* Argument/scratch */
r3		/* Argument/scratch */
r12		/* Intra-procedure/scratch */
lr		/* Return address */
pc
xpsr
```

This is called the **Exception Stack Frame**. (Total: 8 registers = 32 bytes). This frame represents the **exact CPU state at the moment of fault**.

These registers are:

* **Caller-saved (volatile/call-clobbered)** in the ARM procedure call standard
* Sufficient to fully restore execution
* The minimum context needed to resume

### Why a Normal C `HardFault_Handler()` Is Not Enough?

When a C function is invoked, the compiler generates a function prologue in which any **callee-saved (non-volatile/call-preserved)** registers used within the function are pushed onto the stack, in addition to the registers that were already stacked during the exception entry sequence. As a result, it becomes difficult to reliably retrieve the original exception stack frame without first accounting for the additional stack modifications introduced by the compiler-generated prologue.

These callee-saved registers typically include `r4`–`r11`. (The stack pointer (`sp`) itself is not pushed automatically; however, it may be adjusted to allocate space for local variables or additional function arguments beyond the first four (which are passed in `r0`–`r3` according to the ARM calling convention).)
