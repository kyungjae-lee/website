[Home](../../) | [Projects](../../projects) | [Notes](../) > MCU Peripheral Drivers

# MCU Peripheral Drivers



### Introduction to Microcontroller

* **[Memory Map](memory-map)**
* **[MCU Bus Interfaces](mcu-bus-interfaces)**
* **[MCU Block Diagram](mcu-block-diagram)**
* **[Clocks](clocks)**
* **[Vector Table](vector-table)**
* **[Interrupts](interrupts)**
* **[Using `printf()` with Serial Wire Viewer (SWV)](using-printf-with-serial-wire-viewer)**

### MCU Peripheral Driver Development

* **[Architecture & Development Plan](architecture-and-development-plan)**
* **[Device Header File (`stm32f407xx.h`)](device-header-file)**

##### General Purpose Input/Output (GPIO) Driver

* **[Introduction to GPIO](introduction-to-gpio)**
* **[GPIO Driver (`stm32f407xx_gpio_driver.h/.c`)](gpio-driver)**
* **[GPIO Application 1: Toggling On-board LED (`gpio_01_led_toggle_pushpull.c`, `gpio_01_led_toggle_opendrain.c`)](gpio-application-1)**
* **[GPIO Application 2: Toggling On-board LED with On-board Button (`gpio_02_led_toggle_with_button.c`)](gpio-application-2)**
* **[GPIO Application 3: Toggling External LED with External Button (`gpio_03_ext_led_toggle_with_ext_button.c`)](gpio-application-3)**
* **[GPIO Application 4: Toggling LED with External Button Interrupt (`gpio_04_led_toggle_with_ext_button_interrupt.c`)](gpio-application-4)**

##### Serial Peripheral Interface (SPI) Driver

* **[Introduction to SPI](introduction-to-spi)**
* **[Common Problems in SPI & Debugging Tips](common-problems-in-spi-and-debugging-tips)**
* **[SPI Driver (`stm32f407xx_spi_driver.h/.c`)](spi-driver)**
* **[SPI Application 1: Master Only Tx (Blocking) (`spi_01_master_only_tx_blocking.c`)](spi-application-1)**
* **[SPI Application 2: Master Tx (Blocking) (`spi_02_master_tx_blocking.c`)](spi-application-2)**
* **[SPI Application 3: Master Tx Rx (Blocking) (`spi_03_master_tx_rx_blocking.c`)](spi-application-3)**
* **[SPI Application 4: Master Rx (Interrupt) (`spi_04_master_rx_interrupt`)](spi-application-4)**

##### Inter-Integrated Circuit (I2C) Driver

* **[Introduction to I2C](introduction-to-i2c)**
* **[Common Problems in I2C & Debugging Tips](common-problems-in-i2c-and-debugging-tips)**
* **[I2C Driver (`stm32f407xx_i2c_driver.h/.c`)](i2c-driver)**
* **[I2C Application 1: Master Tx (Blocking) (`i2c_01_master_tx_blocking.c`)](i2c-application-1)**
* **[I2C Application 2: Master Rx (Blocking) (`i2c_02_master_rx_blocking.c`)](i2c-application-2)**
* **[I2C Application 3: Master Rx (Interrupt) (`i2c_03_master_rx_interrupt.c`)](i2c-application-3)**
* **[I2C Application 4: Slave Tx (Interrupt) (`i2c_04_slave_tx_interrupt.c`)](i2c-application-4)**

##### Universal Synchronous/Asynchronous Receiver-Transmitter (USART/UART) Driver

* **[Introduction to USART/UART](introduction-to-usart-uart)**
* **[USART Driver (`stm32f407xx_usart_driver.h/.c`)](usart-driver)**
* **[USART Application 1:  Tx (Blocking) (`usart_01_tx_blocking.c`)](usart-application-1)**
* **[USART Application 2:  Tx Rx (Interrupt) (`usart_02_tx_rx_interrupt.c`)](usart-application-2)**

##### Reset and Clock Control (RCC) Driver

* **[RCC Driver (`stm32f407xx_rcc_driver.h/.c`)](rcc-driver)**



### Project: Real-Time Clock on LCD

* **[Introduction to Real-Time Clock on LCD](introduction-to-real-time-clock-on-lcd)**
* **[Real-Time Clock (RTC) (DS1307)](real-time-clock-ds1307)**
* **[16x2 Character LCD (HD44780U)](16x2-character-lcd-hd44780u)**
* **[Board Support Package (BSP) (`rtc_ds1307.h/.c`, `lcd_hd44780u.h/.c`)](board-support-package)**
* **[Application: Real-Time Clock on LCD (`rtc_on_lcd.c`)](application-real-time-clock-on-lcd)**
