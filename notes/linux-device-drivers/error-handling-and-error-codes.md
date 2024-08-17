[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Linux Device Drivers</a> > Error Handling & Error Codes

# Error Handling & Error Codes



## Error Handling

* The Linux kernel coding standard document recommends using the `goto` statement of the C programming language for error handling. (Only for error handling)

  This is just a recommendation!

* Error handling structure using `if...else` statement:

  ```c
  if (try_something_1 != Err)
  {
      if (try_something_2 != Err)
      {
          if (try_something_3 != Err)
          {
              
          }
          else
          {
              undo_try_something_2;
              undo_try_something_1;
          }
      }
      else
      {
          undo_try_something_1;
      }
  }
  ```

  > You must UNDO the previous successful operations when an error is encountered along the way because there might be some resources opened or held by those operations.

* Error handling structure using `goto` statement:

  ```c
  if (try_something_1 == Err)
      goto err1;
  if (try_something_2 == Err)
      goto err2;
  if (try_something_3 == Err)
      goto err3;
  
  err3:
  	undo_try_something_2;
  err2:
  	undo_try_something_1;
  err1:
  	return ret;
  ```

  > Looks cleaner than when using `if...else` statement.

* `if...else` and `goto` statements can also be used in combination.



## Error Handling of Pointers During Kernel Function Return

* Some kernel macros can be used to deal with return of error pointers by kernel functions.
  * The reason why the kernel functions do not simply return `NULL` upon fail is because `NULL` does not give any idea what went well. Instead, the kernel functions are generally designed to return a pointer that can be interpreted to a meaningful value that tells the user what went wrong.
* The following are most frequently used macros (defined in `include/linux/err.h`) help understand what caused the kernel function fail.
  * `IS_ERR()`
  * `PTR_ERR()`
  * `ERR_PTR()`



## Error Codes and Descriptions

* Your device driver (or kernel module) should be designed to return an appropriate error code when an error happens.

* The user space global variable `errno` will be set to that error code from the kernel space so that the user space code is able to understand what has gone wrong in the kernel space.

* Error codes are defined in the file `include/uapi/asm-generic/errno-base.h`

  ```c
  /* SPDX-License-Identifier: GPL-2.0 WITH Linux-syscall-note */
  #ifndef _ASM_GENERIC_ERRNO_BASE_H
  #define _ASM_GENERIC_ERRNO_BASE_H
  
  #define EPERM        1  /* Operation not permitted */
  #define ENOENT       2  /* No such file or directory */
  #define ESRCH        3  /* No such process */
  #define EINTR        4  /* Interrupted system call */
  #define EIO      	 5  /* I/O error */
  #define ENXIO        6  /* No such device or address */
  #define E2BIG        7  /* Argument list too long */
  #define ENOEXEC      8  /* Exec format error */
  #define EBADF        9  /* Bad file number */
  #define ECHILD      10  /* No child processes */
  #define EAGAIN      11  /* Try again */
  #define ENOMEM      12  /* Out of memory */
  #define EACCES      13  /* Permission denied */
  #define EFAULT      14  /* Bad address */
  #define ENOTBLK     15  /* Block device required */
  #define EBUSY       16  /* Device or resource busy */
  #define EEXIST      17  /* File exists */
  #define EXDEV       18  /* Cross-device link */
  #define ENODEV      19  /* No such device */
  #define ENOTDIR     20  /* Not a directory */
  #define EISDIR      21  /* Is a directory */
  #define EINVAL      22  /* Invalid argument */
  #define ENFILE      23  /* File table overflow */
  #define EMFILE      24  /* Too many open files */
  #define ENOTTY      25  /* Not a typewriter */
  #define ETXTBSY     26  /* Text file busy */
  #define EFBIG       27  /* File too large */
  #define ENOSPC      28  /* No space left on device */
  #define ESPIPE      29  /* Illegal seek */
  #define EROFS       30  /* Read-only file system */
  #define EMLINK      31  /* Too many links */
  #define EPIPE       32  /* Broken pipe */
  #define EDOM        33  /* Math argument out of domain of func */
  #define ERANGE      34  /* Math result not representable */
  
  #endif
  ```
