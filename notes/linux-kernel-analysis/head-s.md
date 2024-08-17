[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Kernel Analysis</a> > head.S

# head.S



### Kernel Startup Entry Point

The following comment sets the stage for the kernel's early initialization process, ensuring  that key hardware settings and data structures are appropriately handled before the MMU is enabled and the kernel switches to more advanced memory management and virtual addressing.

```assembly
/*
 * Kernel startup entry point.
 * ---------------------------
 *
 * The requirements are:
 *   MMU = off, D-cache = off, I-cache = on or off,
 *   x0 = physical address to the FDT blob.
 *
 * Note that the callee-saved registers are used for storing variables
 * that are useful before the MMU is enabled. The allocations are described
 * in the entry routines.
 */
```

> Why does the kernel startup entry point not care about the I-cache on/off state?
>
> During the early kernel startup, the primary concern is to ensure that the basic hardware setup, data structures, and essential features like MMU are correctly initialized. The impact of I-cache being on or off might not be critical at this point, and its state can be appropriately handled later in the kernel's initialization process when more advanced  memory management and virtual addressing are in place. Therefore, the code does not specifically require the I-cache state to be either on or off during the early kernel startup with MMU off and D-cache off.
>
> When the MMU is off and the D-cache is off, the kernel operates in a simple flat memory model, where virtual addresses are the same as physical addresses. In this mode, there is no translation of virtual addresses to physical addresses, and memory accesses are performed directly using physical addresses.
>
> The role of the D-cache is to store frequently accessed data to speed up memory access. However, with the D-cache off and MMU off, memory accesses are not cached, so any changes to memory will be immediately visible to all parts of the kernel and other hardware.
>
> On the other hand, the role of the I-cache is to store frequently accessed instructions to speed up instruction fetches. Whether the I-cache is on or off may not have a significant impact at this very early stage of kernel startup. The kernel's initialization code is relatively small and straightforward at this point, and the code execution mainly involves a sequence of instructions that set up essential data structures, manage hardware, and enable features like MMU and caches.
>
> Since the kernel is executed from memory, even if the I-cache is off, the instruction fetches are still likely to be fast because there is no virtual memory translation overhead, and the code is readily available in memory.



### __HEAD

`__INIT` is a macro defined in `include/linux/init.h`:

```c
#define __HEAD      .section    ".head.text","ax"
```

When the code is assembled, the assembly code inside the `__HEAD` macro will be placed in the `.head.text` section with the appropriate flags, ensuring that it is both allocatable (`a`) and marked as executable (`x`) in the final output. The section name and flags can be adjusted based on the specific needs of the project.



### Image Header Expected by Linux Bootloaders

The image header provides essential information for the boot-loader to load and execute the kernel properly. The bootloader recognizes the  signature (EFI signature NOP) and the magic number to identify this as an ARM64 executable. It then uses the entry point (`primary_entry`) to start executing the kernel and considers the load offset, kernel size, and flags to manage memory allocation and other operations correctly.

The image header conforms to the header format defined in the documentation `Documentation/arm64/booting.rst` section 4.

```assembly
    /*  
     * DO NOT MODIFY. Image header expected by Linux boot-loaders.
     */
    efi_signature_nop           @ special NOP to identity as PE/COFF executable
    b   primary_entry           @ Branch to kernel start. The bootloader will use this address
    							@ to start executing the kernel code.
    .quad   0               	@ Image load offset from start of RAM, little-endian
    le64sym _kernel_size_le     @ Effective size of kernel image, little-endian
    le64sym _kernel_flags_le    @ Informative flags, little-endian
    .quad   0               	@ reserved
    .quad   0               	@ reserved
    .quad   0               	@ reserved
    .ascii  ARM64_IMAGE_MAGIC   @ Magic number
    .long   .Lpe_header_offset  @ Offset to the PE header.
```

> L4: `efi_signature_nop` is a macro defined in the file `arch/arm64/kernel/eif-header.S`:
>
> ```assembly
>  .macro  efi_signature_nop                                           
> #ifdef CONFIG_EFI
> .L_head:
>  /*
>      * This ccmp instruction has no meaningful effect except that
>      * its opcode forms the magic "MZ" signature required by UEFI.
>      */
>     ccmp    x18, #0, #0xd, pl	@ ccmp instruction is 0xFA405A4D in hex code and this will 
>     							@ appear as 4D 5A 40 FA in the kernel image built in little-endian
>     							@ mode, first two of which are 'M' and 'Z' respectively
> #else
>     /*  
>      * Bootloaders may inspect the opcode at the start of the kernel
>      * image to decide if the kernel is capable of booting via UEFI.
>      * So put an ordinary NOP here, not the "MZ.." pseudo-nop above.
>      */
>     nop 	@ simply consume one clock cycle, doing nothing meaningful
> #endif
> ```
>



### __INIT

`__INIT` is a macro defined in `include/linux/init.h`:

```c
#define __INIT      .section    ".init.text","ax" 
```

When the code is assembled, the assembly code inside the `__INIT` macro will be placed in the `.init.text` section with the appropriate flags, ensuring that it is both allocatable (`a`) and marked as executable (`x`) in the final output. The section name and flags can be adjusted based on the specific needs of the project.



### record_mmu_state

The `record_mmu_state` subroutine is designed to handle the endianness and MMU state at different exception levels (EL) in the ARM64 processor architecture. The code handles toggling the endianness, clearing the MMU enable bit, and applying workarounds if necessary  before writing the modified values back to the relevant control registers. The subroutine ensures that memory accesses issued before the `init_kernel_el()` function occur in the correct byte order and with the appropriate MMU state.

`record_mmu_state` records the MMU on/off state in `x19` upon return.

* If `x19` == 0, cache maintenance (e.g., invalidation) will be necessary
* If `x19` == 1, cache maintenance will not be necessary

Think of the following four possible scenarios:

1. **Cache on, MMU on (`x19` = 1)**

   Cache invalidation not necessary since the cache will already contain valid data

2. Cache on, MMU off (`x19` = 0)

   Cache invalidation necessary since the validity of the cache contents will not be guaranteed at the moment when the MMU turns on

3. Cache off, MMU on (`x19` = 0)

   Cache invalidation necessary since the validity of the cache contents is not guaranteed

4. Cache off, MMU off (`x19` = 0)

   Cache invalidation necessary since the validity of the cache contents will not be guaranteed at the moment when the MMU turns on

```assembly
SYM_CODE_START_LOCAL(record_mmu_state)
    mrs x19, CurrentEL			@ move the current exception level value into x19
    cmp x19, #CurrentEL_EL2		@ is the current exception level EL2(Hybervisor mode)?
    mrs x19, sctlr_el1
    b.ne    0f  				@ if not (EL2), branch to '0f'
    mrs x19, sctlr_el2
0:
CPU_LE( tbnz    x19, #SCTLR_ELx_EE_SHIFT, 1f    )	@ If the current endianness is LE and the bit
													@ SCTLR_ELx_EE_SHIFT is not 1, branch to '1f'.
CPU_BE( tbz x19, #SCTLR_ELx_EE_SHIFT, 1f    )		@ If the current endianness is BE and the bit
													@ SCTLR_ELx_EE_SHIFT is not 0, branch to '1f'.
    tst x19, #SCTLR_ELx_C       // Z := (C == 0)
    and x19, x19, #SCTLR_ELx_M  // isolate M bit
    csel    x19, xzr, x19, eq   // clear x19 if Z
    ret 

    /*  
     * Set the correct endianness early so all memory accesses issued
     * before init_kernel_el() occur in the correct byte order. Note that
     * this means the MMU must be disabled, or the active ID map will end
     * up getting interpreted with the wrong byte order.
     */
1:  eor x19, x19, #SCTLR_ELx_EE		@ toggle the Endianness Enable bit
    bic x19, x19, #SCTLR_ELx_M		@ clear the MMU enable bit
    b.ne    2f  					@ if EL1, branch to '2f'
    pre_disable_mmu_workaround		@ A placeholder for code that handles a workaround
    								@ before disabling the MMU.							
    msr sctlr_el2, x19 				@ Write the modified x19 back to the 
    								@ System Control Register for EL2 (sctlr_el2).
    b   3f  
2:  pre_disable_mmu_workaround
    msr sctlr_el1, x19 				@ write the modified x19 back to the
    								@ System Control Register for EL2 (sctlr_el1)
3:  isb 
    mov x19, xzr 					@ move zero into x19
    ret 							@ return from the subroutine
SYM_CODE_END(record_mmu_state)
```

>`x19` is typically used to store the value of the System Control Register (SCTLR) during early boot.



### preserve_boot_args

The `preserve_boot_args` subroutine ensures that the  important bootloader arguments are preserved in memory for the kernel's use. Additionally, it handles cache invalidation, especially when the MMU is off, to ensure data consistency during the early stages of the kernel's execution.

```assembly
/*
 * Preserve the arguments passed by the bootloader in x0 .. x3
 */
SYM_CODE_START_LOCAL(preserve_boot_args)
    mov x21, x0             @ x21=FDT; load the Flattened-Device-Tree (FDT) start address into x21

    adr_l   x0, boot_args	@ Load x0 with the address of the label 'boot_args' (this label 
    						@ represents a memory location where the bootloader arguments 
    						@ will be preserved for the kernel's reference).
    						@ 'boot_args' is an array of 4 64-bit elements.
    stp x21, x1, [x0]       @ Store x21 and x1 in memory at the address pointed by x0
    stp x2, x3, [x0, #16]	@ followed by x2 and x3.

    cbnz    x19, 0f         @ if x19 != 0 (i.e., MMU=on), skip the cache invalidation (jump to '0'f)
    dmb sy              	@ Data Memory Barrier (DMB) instruction with the 'sy(system)'
    						@ option. This barrier ensures that all memory accesses before
    						@ it are completed before proceeding. It is used before the 
    						@ cache invalidation operation with the MMU off

    add x1, x0, #0x20       @ Load x1 with 'x0 + 0x20' (4 x 8 bytes after the initial address) to
    						@ prepare x1 to be used in the following tail call.
    b   dcache_inval_poc    @ Tail call that jumps to a cache invalidation function. (The
    						@ purpose of the cache invalidation is to ensure the chache 
    						@ coherency for the preserved dat.)
0:  str_l   x19, mmu_enabled_at_boot, x0	@ The value of x19, which represents whether the
											@ MMU was enabled at boot time, is stored at the
											@ address pointed by x0. This is done after the 
											@ cache invalidation part and is likely used for
											@ reference or debugging purposes.
    ret
SYM_CODE_END(preserve_boot_args)
```

> L7: `adr_l` is a macro defined in `arch/arm64/include/asm/assembler.h`:
>
> ```c
> /*
>  * Pseudo-ops for PC-relative adr/ldr/str <reg>, <symbol> where
>  * <symbol> is within the range +/- 4 GB of the PC.
>  */
>     /*  
>      * @dst: destination register (64 bit wide)
>      * @sym: name of the symbol
>      */
>     .macro  adr_l, dst, sym                                                
>     adrp    \dst, \sym
>     add \dst, \dst, :lo12:\sym
>     .endm
> ```
>
> > Due to the limitation bit-field length in expressing 64-bit address, the macro `adr_l` is introduced. It uses the "page address" and the "offset" to express 64-bit address. (Commonly seen in arm64)
>

Can be written as the following C code:

```c
void preserve_boot_args() {
  boot_args[0] = x21; // fdt
  boot_args[1] = x1;
  boot_args[2] = x2;
  boot_args[3] = x3;
  dcache_inval_proc(boot_args);
  mmu_enabled_at_boot = x19; // MMU on or off
}
```



### dcache_inval_poc (arch/arm64/mm/cache.S)

The function `dcache_inval_poc` is used to invalidate and clean (flush) the data cache for a specific memory range [start, end). It handles cache-line alignment for both the start and end addresses and uses the `civac` and `ivac` cache maintenance instructions to perform cleaning and invalidation operations, respectively. The function iterates over each cache line in  the specified range and ensures data coherency between the cache and memory.

```assembly
/*
 *  dcache_inval_poc(start, end)                         
 *
 *  Ensure that any D-cache lines for the interval [start, end)
 *  are invalidated. Any partial lines at the ends of the interval are
 *  also cleaned to PoC to prevent data loss.
 *
 *  - start   - kernel start address of region
 *  - end     - kernel end address of region
 */
SYM_FUNC_START(__pi_dcache_inval_poc)
    dcache_line_size x2, x3	@ Load the size of a cache line into x2. (x3 will be used as a
    						@ scratch register inside this macro to hold the value of 
    						@ 'ctr_el0' bits[19:16].
    sub x3, x2, #1		@ Get x3 ready to be used as a bit-mask to mask off the lower bits of
    					@ ensure cache-line alignment.
    tst x1, x3          @ Is the x1 (end address of 'boot_args') cache line-aligned?
    bic x1, x1, x3		@ Mask off the lower bits of x1 using x3 to make it cache line-aligned.
    					@ e.g., If the cache line size is 64 bytes and x1 contained 70, then x1
    					@ will be adjusted to 64. (Lower 6 bits dropped)
    b.eq    1f  		@ If the end address is already cache line-aligned, skip the cache 
    					@ maintenance and jump to label 1.
    dc  civac, x1       @ Clean and invalidate the D-cache line where the end address of 
    					@ 'boot_args' is located. The 'civac' cache maintenance operation 
    					@ combines cleaning (write-back)and invalidation of a single cache line.
1:  tst x0, x3          @ Is the x0 (start address of 'boot_args') cache line-aligned?
    bic x0, x0, x3		@ Mask off the lower bits of x0 using x3 to make it cache line-aligned.
    b.eq    2f  		@ If the start address is already cache line-aligned, jump to label 2 
    					@ to perform cache invalidation only.
    dc  civac, x0       @ Clean and invalidate the data cache for the start address x0.
    b   3f  			@ Branch to label 3 to perform the cache invalidation for the remaining 
    					@ memory range.
2:  dc  ivac, x0        @ Invalidate the data cache for the start address x0. This instruction
						@ invalidates the cache line without writing its contents back to memory.
3:  add x0, x0, x2		@ Move the start address x0 forward by the cache line size to handle the
						@ next cache line.
    cmp x0, x1			@ Compare the updated start address x0 with the end address x1.
    b.lo    2b  		@ If x0 is less than x1, loop back to label 2 to continue the cache
    					@ invalidation for the remaining cache lines.
    dsb sy				@ Data Synchronization Barrier (dsb) instruction with the sy (system)
    					@ option. It ensures that all explicit memory accesses before the barrier 
    					@ are completed before proceeding.
    ret 
SYM_FUNC_END(__pi_dcache_inval_poc)
SYM_FUNC_ALIAS(dcache_inval_poc, __pi_dcache_inval_poc)
```

> `SYM_FUNC_START(function_name)` is a symbol defining the start of the `function_name` function. It is typically used for debugging and symbol tracking purposes.
>
> L12: `dcache_line_size` is a macro defined in `arch/arm64/include/asm/assembler.h `.
>
> ```c
> /*
>  * dcache_line_size - get the safe D-cache line size across all CPUs                         
>  */
>     .macro  dcache_line_size, reg, tmp 
>     read_ctr	\tmp				// Read the Cache Type Register into \tmp.
>     ubfm	\tmp, \tmp, #16, #19	// Extract the bits [19:16] from \tmp and store the
>        								// result back in \tmp.
>     mov		\reg, #4        		// Bytes per word.
>     lsl		\reg, \reg, \tmp    	// reg <<= tmp; (Actual cache line size in bytes).
>     .endm							// End of the macro definition.
> ```

C representation of this function block:

```c
x2 = dcache_line_size();
x3 = x2  - 1;
if(x1 & x3 != 0) {      // end cache line aligned?
        // not aligned
        x1 &= ~x3;
        dc_civac(x1);   // clean & invalidate D / U line
}
// 1:
if(x0 & x3 != 0) {      // start cache line aligned?
        // not aligned
        x0 &= ~x3;
        dc_civac(x0);   // clean & invalidate D / U line
}

// 2: 3:
do {
        dc_ivac(x0);
        x0 += x2;       // x0 += dcache_line_size
} while(x0 < x1) {      // cmp     x0, x1;      b.lo    2b;
dsb_sy();
return;
```



### create_idmap

`create_idmap` is responsible for setting up the identity mapping (ID map) for  specific memory regions in the ARM64 Linux kernel. The ID map establishes a 1:1 mapping between physical addresses and virtual  addresses, allowing direct access to physical memory using virtual addresses.

```assembly
SYM_FUNC_START_LOCAL(create_idmap)
    mov x28, lr
    /*
     * The ID map carries a 1:1 mapping of the physical address range
     * covered by the loaded image, which could be anywhere in DRAM. This
     * means that the required size of the VA (== PA) space is decided at
     * boot time, and could be more than the configured size of the VA
     * space for ordinary kernel and user space mappings.
     *
     * There are three cases to consider here:
     * - 39 <= VA_BITS < 48, and the ID map needs up to 48 VA bits to cover
     *   the placement of the image. In this case, we configure one extra
     *   level of translation on the fly for the ID map only. (This case
     *   also covers 42-bit VA/52-bit PA on 64k pages).
     *
     * - VA_BITS == 48, and the ID map needs more than 48 VA bits. This can
     *   only happen when using 64k pages, in which case we need to extend
     *   the root level table rather than add a level. Note that we can
     *   treat this case as 'always extended' as long as we take care not
     *   to program an unsupported T0SZ value into the TCR register.
     *
     * - Combinations that would require two additional levels of
     *   translation are not supported, e.g., VA_BITS==36 on 16k pages, or
     *   VA_BITS==39/4k pages with 5-level paging, where the input address
     *   requires more than 47 or 48 bits, respectively.
     */
#if (VA_BITS < 48)
#define IDMAP_PGD_ORDER (VA_BITS - PGDIR_SHIFT)
#define EXTRA_SHIFT (PGDIR_SHIFT + PAGE_SHIFT - 3)

    /*
     * If VA_BITS < 48, we have to configure an additional table level.
     * First, we have to verify our assumption that the current value of
     * VA_BITS was chosen such that all translation levels are fully
     * utilised, and that lowering T0SZ will always result in an additional
     * translation level to be configured.
     */
#if VA_BITS != EXTRA_SHIFT
#error "Mismatch between VA_BITS and page size/number of translation levels"
#endif
#else
#define IDMAP_PGD_ORDER (PHYS_MASK_SHIFT - PGDIR_SHIFT)
#define EXTRA_SHIFT
    /*
     * If VA_BITS == 48, we don't have to configure an additional
     * translation level, but the top-level table has more entries.
     */
#endif
    adrp    x0, init_idmap_pg_dir
    adrp    x3, _text
    adrp    x6, _end + MAX_FDT_SIZE + SWAPPER_BLOCK_SIZE
    mov x7, SWAPPER_RX_MMUFLAGS

    map_memory x0, x1, x3, x6, x7, x3, IDMAP_PGD_ORDER, x10, x11, x12, x13, x14, EXTRA_SHIFT

    /* Remap the kernel page tables r/w in the ID map */
    adrp    x1, _text
    adrp    x2, init_pg_dir
    adrp    x3, init_pg_end
    bic x4, x2, #SWAPPER_BLOCK_SIZE - 1
    mov x5, SWAPPER_RW_MMUFLAGS
    mov x6, #SWAPPER_BLOCK_SHIFT
    bl  remap_region

    /* Remap the FDT after the kernel image */
    adrp    x1, _text
    adrp    x22, _end + SWAPPER_BLOCK_SIZE
    bic x2, x22, #SWAPPER_BLOCK_SIZE - 1
    bfi x22, x21, #0, #SWAPPER_BLOCK_SHIFT      // remapped FDT address
    add x3, x2, #MAX_FDT_SIZE + SWAPPER_BLOCK_SIZE
    bic x4, x21, #SWAPPER_BLOCK_SIZE - 1
    mov x5, SWAPPER_RW_MMUFLAGS
    mov x6, #SWAPPER_BLOCK_SHIFT
    bl  remap_region

    /*
     * Since the page tables have been populated with non-cacheable
     * accesses (MMU disabled), invalidate those tables again to
     * remove any speculatively loaded cache lines.
     */
    cbnz    x19, 0f             // skip cache invalidation if MMU is on
    dmb sy

    adrp    x0, init_idmap_pg_dir
    adrp    x1, init_idmap_pg_end
    bl  dcache_inval_poc
0:  ret x28
SYM_FUNC_END(create_idmap)
```



### create_idmap (arch/arm64/mm/mmu.c)

The function `create_idmap()` is used to create an identity mapping for a specific memory region, called the ID map, in the ARM64 Linux kernel. The identity mapping allows direct access to physical memory using virtual addresses without any translation.

```c
static void __init create_idmap(void)
{
    /* '__pa_symbol' is a macro that retrieves the physical address of a symbol. */
    u64 start = __pa_symbol(__idmap_text_start);
    u64 size = __pa_symbol(__idmap_text_end) - start;
    /*
     * Declare a pointer 'pgd' which points to a Page Global Directory (PGD) structure 
     * and initialize it with the address of the 'idmap_pg_dir', which points to the 
     * Page Global Directory for the ID map.
     */
    pgd_t *pgd = idmap_pg_dir;
    u64 pgd_phys;	// Declare a variable to store the physical address of the PGD.

    /* 
     * The following code block checks if an additional level of translation is needed
     * for the ID map.
     * - 'VA_BITS' represents the number of virtual address bits.
     * - 'idmap_t0sz' is a kernel configuration parameter that sets the size 
     *   of the top-level translation table (TTBR0) for the ID map.
     */
    if (VA_BITS < 48 && idmap_t0sz < (64 - VA_BITS_MIN)) {
        /* Allocate memory for the PGD and store its PA into 'pgd_phys'. */
        pgd_phys = early_pgtable_alloc(PAGE_SHIFT);
        /*
         * Set the entry in the PGD corresponding to the start address of the ID map. 
         * It configures the PGD entry to point to a new level of translation table (P4D)
         * using the physical address pgd_phys and sets the type to indicate that it's a 
         * table entry.
         */
        set_pgd(&idmap_pg_dir[start >> VA_BITS], __pgd(pgd_phys | P4D_TYPE_TABLE));
        /* Convert the PA 'pgd_phys' to a VA using the '__va' macro and store it into 'pgd'. */
        pgd = __va(pgd_phys);
    }
    /*
     * '__create_pgd_mapping' is a helper function that creates a mapping between 
     * the virtual addresses and physical addresses in the PGD. It sets up the identity
     * mapping for the ID map by configuring the PGD entries.
     */
    __create_pgd_mapping(pgd, start, start, size, PAGE_KERNEL_ROX, early_pgtable_alloc, 0);

    /* 
     * The following code block creates a mapping for the KPTI (Kernel Page Table Isolation)
     * synchronization flag if the CONFIG_UNMAP_KERNEL_AT_EL0 kernel configuration is enabled.
     * - 'CONFIG_UNMAP_KERNEL_AT_EL0' is a kernel configuration option that enables the
     *   KPTI feature.
     * - '__idmap_kpti_flag' is a symbol representing the KPTI synchronization flag.
     */
    if (IS_ENABLED(CONFIG_UNMAP_KERNEL_AT_EL0)) {
        extern u32 __idmap_kpti_flag;
        u64 pa = __pa_symbol(&__idmap_kpti_flag);

        /*   
         * '__create_pgd_mapping()' function is called again to create a mapping between
         * the virtual and physical addresses for the KPTI synchronization flag. It is
         * mapped as read-write with the 'PAGE_KERNEL' flag.
         */
        __create_pgd_mapping(pgd, pa, pa, sizeof(u32), PAGE_KERNEL,
                     early_pgtable_alloc, 0);
    }    
}
```
