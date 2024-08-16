[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Bootloader</a> > Introduction to Bootloader

# Introduction to Bootloader



## What is a Bootloader?

* A bootloader is a small piece of software that is responsible for  starting up a computer system and loading the operating system into the  computer's memory.

* It is typically the first program that runs when you  power on or restart your computer.

* It is typically stored in the Flash or ROM of the MCU to load the application or provide a mechanism to update the applications whenever required.

* Examples: 

  1. Arduino Uno Rev3 board (ATmega328P)

     * Its MCU comes with an on-chip bootloader

     * Upon reset, its bootloader runs first

     * The main purpose of its bootloader is to download the Arduino sketches to the board. (IAP)
     * This board does not have an **In-Circuit Debugger/Programmer (ICDP)** circuitry. So, you have to do in-application programming which uses the bootloader in order to flash the binary.

  2. STM32F446RE Necleo 64 board

     * Its MCU comes with an on-chip bootloader

     * By default, its bootloader won't run upon MCU reset. It requires some modifications of the boot pins to make the bootloader run upon every MCU reset.

     * The main purpose of its bootloader is to download/upload the binaries (IAP).

     * This board has an **In-Circuit Debugger/Programmer (ICDP)** circuitry (i.e., ST-Link) which allows programming the board without the bootloader. This is why this board does not run the bootloader by default upon reset.

       This is called "In-System Programming (ISP)"



## Why is the Bootloader Necessary?

* If your board or product doesn't support in-circuit debugging/programming circuitry, then you have to use the bootloader in order to update or write a new application binary into the board.

