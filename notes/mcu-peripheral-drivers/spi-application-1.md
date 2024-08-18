[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">MCU Peripheral Drivers</a> > SPI Application 1: Master Only Tx (Blocking) (`spi_01_master_only_tx_blocking.c`)

# SPI Application 1: Master Only Tx (Blocking) (`spi_01_master_only_tx_blocking.c`)



## Requirements

* Test the `SPI_TxBlocking)` API to send the string "Hello world" with the following configurations:
  * SPI2 - Master mode
  * SCLK - Max possible
  * DFF = 0, and DFF = 1
* For this application, only MOSI and SCLK pins will be used. (No slave, so MISO, NSS pins are not necessary.) Find out the GPIO pins over which SPI2 can communicate! Look up the "Alternate function mapping" table in the datasheet.
  * **SPI2_SCK $\to$ PB13 (AF5)**
  * **SPI2_MOSI $\to$ PB15 (AF5)**
  *  SPI2_MISO $\to$ PB14 (AF5)
  * SPI2_NSS $\to$ PB12 (AF5)
* To analyze the communication with the logic analyzer, connect the channels as follows:
  * CH0 - SCLK
  * CH1 - MOSI




## Code

### `spi_01_master_only_tx_blocking.c`

Path: `Project/Src/`

```c
/*******************************************************************************
 * File		: spi_01_master_only_tx_blocking.c
 * Brief	: Program to test SPI master's Tx (blocking) functionality
 * 			  (without slave)
 * Author	: Kyungjae Lee
 * Date		: May 27, 2023
 ******************************************************************************/

/**
 * Pin selection for SPI communication
 *
 * SPI2_SCK  - PB13 (AF5)
 * SPI2_MOSI - PB15 (AF5)
 * SPI2_MISO - PB14 (AF5)
 * SPI2_NSS  - PB12 (AF5)
 */

#include <string.h> 		/* strlen() */
#include "stm32f407xx.h"

/**
 * SPI2_PinsInit()
 * Brief	: Initializes and configures GPIO pins to be used as SPI2 pins
 * Param	: None
 * Retval	: None
 * Note		: N/A
 */
void SPI2_PinsInit(void)
{
	GPIO_Handle_TypeDef SPI2Pins;

	/* Zero-out all the fields in the structures (Very important! SPI2Pins
	 * is a local variables whose members may be filled with garbage values before
	 * initialization. These garbage values may set (corrupt) the bit fields that
	 * you did not touch assuming that they will be 0 by default. Do NOT make this
	 * mistake!
	 */
	memset(&SPI2Pins, 0, sizeof(SPI2Pins));

	SPI2Pins.pGPIOx = GPIOB;
	SPI2Pins.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_ALTFCN;
	SPI2Pins.GPIO_PinConfig.GPIO_PinAltFcnMode = 5;
	SPI2Pins.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_PP;
		/* I2C - Open-drain only!, SPI - Push-pull okay! */
	SPI2Pins.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;	/* Optional */
	SPI2Pins.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH; /* Medium or slow ok as well */

	/* SCLK */
	SPI2Pins.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_13;
	GPIO_Init(&SPI2Pins);

	/* MOSI */
	SPI2Pins.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_15;
	GPIO_Init(&SPI2Pins);

	/* MISO (Not required for this application, save it for other use) */
	//SPI2Pins.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_14;
	//GPIO_Init(&SPI2Pins);

	/* NSS (Not required for this application, save it for other use) */
	//SPI2Pins.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_12;
	//GPIO_Init(&SPI2Pins);
} /* End of SPI2_PinsInit */

/**
 * SPI2_Init()
 * Brief	: Creates an SPI2Handle and initializes SPI2 peripheral parameters
 * Param	: None
 * Retval	: None
 * Note		: N/A
 */
void SPI2_Init(void)
{
	SPI_Handle_TypeDef SPI2Handle;

	/* Zero-out all the fields in the structures (Very important! SPI2Handle
	 * is a local variables whose members may be filled with garbage values before
	 * initialization. These garbage values may set (corrupt) the bit fields that
	 * you did not touch assuming that they will be 0 by default. Do NOT make this
	 * mistake!
	 */
	memset(&SPI2Handle, 0, sizeof(SPI2Handle));

	SPI2Handle.pSPIx = SPI2;
	SPI2Handle.SPI_Config.SPI_BusConfig = SPI_BUS_CONFIG_FULL_DUPLEX;
	SPI2Handle.SPI_Config.SPI_DeviceMode = SPI_DEVICE_MODE_MASTER;
	SPI2Handle.SPI_Config.SPI_SCLKSpeed = SPI_SCLK_SPEED_PRESCALAR_2;	/* Generates 8MHz SCLK */
		/* Min prescalar -> maximum clk speed */
	SPI2Handle.SPI_Config.SPI_DFF = SPI_DFF_8BITS;
	SPI2Handle.SPI_Config.SPI_CPOL = SPI_CPOL_LOW;
	SPI2Handle.SPI_Config.SPI_CPHA = SPI_CPHA_LOW;
	SPI2Handle.SPI_Config.SPI_SSM = SPI_SSM_EN; /* SW slave mgmt enabled for NSS pin since NSS is not used */

	SPI_Init(&SPI2Handle);
} /* End of SPI2_Init */


int main(int argc, char *argv[])
{
	char msg[] = "Hello world";		/* Message to transnmit */

	/* Initialize and configure GPIO pins to be used as SPI2 pins */
	SPI2_PinsInit();

	/* Initialize SPI2 peripheral parameters */
	SPI2_Init();
		/* At this point, all the required parameters are loaded into SPIx control registers.
		 * But, this does not mean that SPI2 peripheral is enabled.
		 *
		 * SPI configuration must be completed before it is enabled. When SPI is enabled, it
		 * will be busy communicating with other device(s) and will not allow modifying its
		 * control registers.
		 */

	/* To make NSS signal pulled to high internally and avoid MODF error */
	SPI_SSIConfig(SPI2, ENABLE);

	/* Enable SPI2 peripheral (Set SPI_CR1 bit[6] SPE - Peripheral enabled) */
	SPI_PeriControl(SPI2, ENABLE);

	/* Send data */
	SPI_TxBlocking(SPI2, (uint8_t *)msg, strlen(msg));

	/* Wait until SPI no longer busy */
	while (SPI2->SR & (0x1 << SPI_SR_BSY));
		/* SPI_SR bit[7] - BSY (Busy flag)
		 * 0: SPI (or I2S) not busy
		 * 1: SPI (or I2S) is busy in communication or Tx buffer is not empty
		 * This flag is set and cleared by hardware.
		 */

	/* Disable SPI2 peripheral (Terminate communication) */
	SPI_PeriControl(SPI2, DISABLE);

	while (1);

	return 0;
} /* End of main */
```



## Testing

The following snapshots are taken using the Logic Analyzer.



### SPI Transmission when CPOL=0, CPHA=0

The following snapshot shows "Hello world" being transferred through MOSI line.

* Since CPOL = 0, SCLK begins LOW, and stays LOW during the idle state.
* Since CPHA = 0, the data appears on the MOSI line during the 1st edge of the SCLK.

<img src="./img/spi-application-1-logic-analyzer-1.png" alt="spi-application-1-logic-analyzer-1" width="1000">

<img src="./img/spi-application-1-logic-analyzer-2.png" alt="spi-application-1-logic-analyzer-2" width="1000">



Try different combinations of CPOL and CPHA and monitor the digital waveform.



## Note

* Here we used SSM (software Slave Management) is enabled. For master, the NSS signal should be tied to +VCC when not used to avoid MODF error which happens in multi-master situation. So, be sure to set SSI bit to pull NSS pin to +VCC internally.

* Remember!

  SSI bit influences NSS state when SSM = 1. By default, SSI = 0, so NSS will be pulled low which is not acceptable for master when working in non-multi-master situation.
