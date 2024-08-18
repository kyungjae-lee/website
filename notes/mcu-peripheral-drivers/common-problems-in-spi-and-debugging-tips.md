[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">MCU Peripheral Drivers</a> > Common Problems in SPI & Debugging Tips

# Common Problems in SPI & Debugging Tips



## Cross-Check Points

* When configure a device as a master in SPI communication, make sure that the device's **master selection** is correctly configured. 

  e.g., In the case of STM32F407 Discovery board, check the `MSTR`(Master selection) bit of the `SPI_CR1` register.

* SPI peripheral clock must be enabled. By default, clocks to almost all peripherals in the MCU are disabled to save power.

  e.g., In the case of STM32F407 Discovery board, check the `SPI2EN` bit of the `RCC_APB1ENR` register.

* SPI peripheral itself must be enabled.

  e.g., In the case of STM32F407 Discovery board, check the `SPE` bit of the `SPI_CR1` register.



## Common Problems in SPI

* **Case 1: Master cannot produce clock and data.**

  * Cause: Improper alternate functionality configuration of the I/O pins

    Action: Double-check the GPIO configuration registers to see if they are configured to the proper values

  * Cause: Configuration overwriting (Properly configured register values can be corrupted by the subsequent function calls or so)

    Action: Dump all the relevant registers' contents just before the transmission begins and do the validity check.

* **Case 2: Master is sending data, but slave is not receiving anything.**

  * Cause: Not pulling down the slave select pin (NSS) to GND before sending data to the slave (If the NSS pin is not pulled down, which means that it is in floating or HI-Z state, the slave will not respond to any data communication.)

    Action: Check the status of the NSS pin.

  * Cause: Improper alternate functionality configuration of the I/O pins

    Action: Double-check the GPIO configuration registers to see if they are configured to the proper values

  * Cause: Not enabling the peripheral IRQ number in NVIC

    Action: Probe the NVIC interrupt mask register to see whether the bit position corresponding to the IRQ number is set or not.

* **Case 3: SPI interrupts are not getting triggered.**

  * Cause: Not enabling the `TXE` or `RXNE` interrupt in the SPI control register.

    Action: Check the SPI control register to see if `TXEIE` and `RXNEIE` bits are set to enable the corresponding interrupts.

  * Cause: Not enabling the peripheral IRQ number in NVIC

    Action: Probe the NVIC interrupt mask register to see whether the bit position corresponding to the IRQ number is set or not.

* **Case 4: Master is producing correct data, but slave is receiving the different data.**

  * Cause: Using long wires in high frequency communication

    Action: Use shorter wires or reduce the SPI serial frequency to 500 KHz or so and monitor the results.
