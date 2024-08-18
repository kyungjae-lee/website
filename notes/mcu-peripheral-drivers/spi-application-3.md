[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">MCU Peripheral Drivers</a> > SPI Application 3: Master-Slave Tx Rx (Blocking) (`spi_03_master_tx_rx_blocking.c`)

# SPI Application 3: Master Tx Rx (Blocking) (`spi_03_master_tx_rx_blocking.c`)



## Requirements

* SPI master(STM) and SPI slave(Arduino) command & response based communication
* When the button on the master is pressed, master shall send a command to the slave and slave shall respond as per the command implementation.
* Requirements
  1. Use SPI full duplex mode
  2. ST board will be in SPI master mode, and Arduino board will be in SPI slave mode.
  3. Use DFF = 0
  4. Use hardware slave management (SSM = 0)
  5. SCLK speed = 500 KHz, f~clk~ = 16MHz
* The slave does not know how many bytes of data master is going to send. So, the master must first send the number of bytes the slave should expect to receive.

### Parts Needed

1. Arduino board
2. STM32 board
3. Logic level converter
4. Breadboard and jumper wires

### STM32 Board and Arduino Board Communication Interfaces



<img src="./img/spi-application-3-communication-interfaces.png" alt="spi-application-3-communication-interfaces" width="500">



### STM32 Board and Arduino Board Voltage Levels

* To work around the voltage level difference, a **logic level shifter** will be necessary.



<img src="./img/spi-application-2-stm32-arduino-voltage-levels.png" alt="spi-application-2-stm32-arduino-voltage-levels" width="700">



### Master - Slave Communication

* Communication sequence:

  1. Master sends a command to slave.

  2. Slave responds with `ACK(0xF5)` or `NACK(0xA5)` byte.

  3. If master recieves `ACK`, it sends 1 or more command arguments.

     * Slave takes action and responds to master according to the command/arguments it has received

     If master receives `NACK`, it displays error message.

* Command formats

  Following commands are provided by the Arduino sketch

  ```plain
  <command_code(1)>		<arg1>					<arg2>
  ======================	======================	=============
  CMD_LED_CTRL			<pin no(1)>				<value(1)> (ON/OFF)
  CMD_SENSOR_READ			<analog pin number(1)>
  CMD_LED_READ			<pin no(1)>
  CMD_PRINT				<len(2)>				<message(len)>
  CMD_ID_READ
  ```

  > `CMD_LED_CTRL <pin no> <value>` is the command to turn on/off LED connected to a specific Arduino pin
  >
  > * `<pin no>` - Digital pin number of the Arduino board (0 to 9) (1 byte)
  > * `<value>` - 1 = ON, 0 = OFF (1 byte)
  >
  > Slave action: Controls the digital pin on/off
  >
  > Slave returns: Nothing

  > `CMD_SENSOR_READ <analog pin number>`
  >
  > * `<analog pin number>` - Analog pin number of the Arduino board (A0 to A5) (1 byte)
  >
  > Slave action: Slave shall read the analog value of the supplied pin
  >
  > Slave returns: 1 byte of analog read value

  > `CMD_LED_READ <pin no>`
  >
  > * `<pin no>` - Digital pin number of the Arduino board (0 to 9)
  >
  > Slave action: Reads the status of the supplied pin number
  >
  > Slave returns: 1 byte of LED status (1 = ON, 0 = OFF)

  > `CMD_PRINT <len> <message>`
  >
  > * `<len>` - 1 byte of length information of the message to follow
  > * `<message>` - Message of `len` bytes
  >
  > Slave action: Receives the message and displays it via serial port
  >
  > Slave returns: Nothing

  > `CMD_ID_READ`
  >
  > Slave returns: 10 bytes of board ID string

### Application Flow Chart



<img src="./img/spi-application-3-flow-chart.png" alt="sspi-application-3-flow-chart" width="350">



### Using `printf()` to Print Messages in STM32CubeIDE Console

* See <a href="./using-printf-with-serial-wire-viewer">Using `printf()` with Serial Wire Viewer (SWV)</a>



## Setup

### 1. Find out the GPIO pins that can be used for SPI2 communication

* For this application, all four SPI communication lines (i.e., MOSI, MISO, SCK and NSS pins) will be used. Find out the GPIO pins over which SPI2 can communicate! Look up the "Alternate function mapping" table in the datasheet.
  * **SPI2_MOSI $\to$ PB15 (AF5)**
  * **SPI2_SCK $\to$ PB13 (AF5)**
  * **SPI2_MISO $\to$ PB14 (AF5)**
  * **SPI2_NSS $\to$ PB12 (AF5)**

### 2. Connect STM32 Discovery board with Arduino Uno board SPI pins

* Be careful not to directly supply 5 volts to the STM32 board pins when the board is not powered up as they may be damaged. When the **logic level shifter** is used, you don't need to worry about this issue.



<img src="./img/spi-application-3-hardware-setup.png" alt="spi-application-3-hardware-setup" width="900">



* To analyze the communication with the logic analyzer, connect the channels as follows:
  * CH0 - SCLK
  * CH1 - MOSI
  * CH2 - MISO
  * CH3 - NSS
  * GND - Common GND of the bread board

### 3. Power Arduino board and upload SPI slave sketch to Arduino

* Sketch name: `002SPISlaveCmdHandling.ino`
  As soon as you download this sketch to the Arduino board, it will operate as a slave.




## Code

### `spi_03_master_tx_rx_blocking.c`

Path: `Project/Src/`

```c
/*******************************************************************************
 * File		: spi_03_master_tx_rx_blocking.c
 * Brief	: Program to test SPI master-slave Tx/Rx functionality (blocking)
 * Author	: Kyungjae Lee
 * Date		: Jun 03, 2023
 ******************************************************************************/

/**
 * Pin selection for SPI communication
 *
 * SPI2_SCK  - PB13 (AF5)
 * SPI2_MOSI - PB15 (AF5)
 * SPI2_MISO - PB14 (AF5)
 * SPI2_NSS  - PB12 (AF5)
 */

#include <stdio.h>			/* printf() */
#include <string.h> 		/* strlen() */
#include "stm32f407xx.h"

/* Arduino (slave) command codes */
#define CMD_LED_CTRL		0x50
#define CMD_SENSOR_READ		0x51
#define CMD_LED_READ		0x52
#define CMD_PRINT			0x53
#define CMD_ID_READ			0x54

#define LED_OFF				0
#define LED_ON				1

/* Arduino analog pin */
#define ANALOG_PIN0			0
#define ANALOG_PIN1			1
#define ANALOG_PIN2			2
#define ANALOG_PIN3			3
#define ANALOG_PIN4			4

/* Arduino LED pin */
#define LED_PIN 			9

/**
 * delay()
 * Brief	: Spinlock delays the program execution
 * Param	: None
 * Retval	: None
 * Note		: N/A
 */
void delay(void)
{
	/* Appoximately ~200ms delay when the system clock freq is 16 MHz */
	for (uint32_t i = 0; i < 500000 / 2; i++);
} /* End of Delay */

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

	/* MISO */
	SPI2Pins.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_14;
	GPIO_Init(&SPI2Pins);

	/* NSS */
	SPI2Pins.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_12;
	GPIO_Init(&SPI2Pins);
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
    SPI2Handle.SPI_Config.SPI_SCLKSpeed = SPI_SCLK_SPEED_PRESCALAR_32;  /* Generates 500KHz SCLK */
		/* Min prescalar -> maximum clk speed */
	SPI2Handle.SPI_Config.SPI_DFF = SPI_DFF_8BITS;
	SPI2Handle.SPI_Config.SPI_CPOL = SPI_CPOL_LOW;
	SPI2Handle.SPI_Config.SPI_CPHA = SPI_CPHA_LOW;
	SPI2Handle.SPI_Config.SPI_SSM = SPI_SSM_DI; /* HW slave mgmt enabled (SSM = 0) for NSS pin */

	SPI_Init(&SPI2Handle);
} /* End of SPI2_Init */

/**
 * GPIO_ButtonInit()
 * Brief	: Initializes a GPIO pin for button
 * Param	: None
 * Retval	: None
 * Note		: N/A
 */
void GPIO_ButtonInit(void)
{
	GPIO_Handle_TypeDef GPIOBtn;

	/* Zero-out all the fields in the structures (Very important! GPIOLed and GPIOBtn
	 * is a local variables whose members may be filled with garbage values before
	 * initialization. These garbage values may set (corrupt) the bit fields that
	 * you did not touch assuming that they will be 0 by default. Do NOT make this
	 * mistake!
	 */
	memset(&GPIOBtn, 0, sizeof(GPIOBtn));

	/* GPIOBtn configuration */
	GPIOBtn.pGPIOx = GPIOA;
	GPIOBtn.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_0;
	GPIOBtn.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_IN;
	GPIOBtn.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH; /* Doesn't matter */
	//GPIOBtn.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_PP;	/* N/A */
	GPIOBtn.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;
		/* External pull-down resistor is already present (see the schematic) */
	GPIO_Init(&GPIOBtn);
} /* End of GPIO_ButtonInit */

/**
 * GPIO_LEDInit()
 * Brief	: Initializes a GPIO pin for LED
 * Param	: None
 * Retval	: None
 * Note		: N/A
 */
void GPIO_LEDInit(void)
{
	GPIO_Handle_TypeDef GPIOLed;

	/* Zero-out all the fields in the structures (Very important! GPIOLed
	 * is a local variable whose members may be filled with garbage values before
	 * initialization. These garbage values may set (corrupt) the bit fields that
	 * you did not touch assuming that they will be 0 by default. Do NOT make this
	 * mistake!
	 */
	memset(&GPIOLed, 0, sizeof(GPIOLed));

	/* GPIOLed configuration */
	GPIOLed.pGPIOx = GPIOD;
	GPIOLed.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_12;
	GPIOLed.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_OUT;
	GPIOLed.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH; /* Doesn't matter */
	GPIOLed.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_OD;
	GPIOLed.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;
		/* External pull-down resistor is already present (see the schematic) */
	GPIO_Init(&GPIOLed);
} /* End of GPIO_LEDInit */

/**
 * SPI_VerifyResponse()
 * Brief	: Verifies response from the slave (Arduino)
 * Param	: @ackByte - response from slave
 * Retval	: 1 if response was ACK, 0 otherwise
 * Note		: N/A
 */
uint8_t SPI_VerifyResponse(uint8_t ackByte)
{
	/* ACK */
	if (ackByte == 0xF5)
		return 1;
	/* NACK */
	else
		return 0;
} /* End of SPI_VerifyResponse */


int main(int argc, char *argv[])
{
	uint8_t dummyWrite = 0xFF;
	uint8_t dummyRead;

	printf("Application is running...\n");

	/* Initialize and configure GPIO pin for user button */
	GPIO_ButtonInit();

	/* Initialize and configure GPIO pin for LED */
	GPIO_LEDInit();

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

	printf("SPI initialized\n");

	/* Enable NSS output (Set SPI_CR2 bit[2] SSOE - Slave Select Output Enable) */
	SPI_SSOEConfig(SPI2, ENABLE);
		/* Setting SSOE bit to 1 enables the NSS output.
		 * The NSS pin is automatically managed by the hardware.
		 * i.e., When SPE = 1, NSS will be pulled to low, and when SPE = 0, NSS will be
		 * pulled to high.
		 */

	while (1)
	{
		/* Wait until button is pressed */
		while (!GPIO_ReadFromInputPin(GPIOA, GPIO_PIN_0));

		/* Introduce debouncing time for button press */
		delay();

		/* Enable SPI2 peripheral (Set SPI_CR1 bit[6] SPE - Peripheral enabled) */
		SPI_PeriControl(SPI2, ENABLE);

		/* 1. CMD_LED_CTRL <pin no(1)> <value(1)> ----------------------------*/

		uint8_t cmdCode = CMD_LED_CTRL;
		uint8_t ackByte;
		uint8_t args[2];

		/* Send command */
		SPI_TxBlocking(SPI2, &cmdCode, 1);
			/* Remember! In SPI communication, when master or slave sends 1 byte of data
			 * it also receives 1 byte in return.
			 *
			 * This transmission of 1 byte results in 1 garbage byte collection in
			 * Rx buffer of the master and therefore RXNE flag will be set. So, do the
			 * dummy read and clear the flag.
			 */

		/* Dummy read to clear the RXNE bit */
		SPI_RxBlocking(SPI2, &dummyRead, 1);

		/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
		 * (To init the communication)
		 */
		SPI_TxBlocking(SPI2, &dummyWrite, 1);
			/* When this API call returns, response from the slave would've arrived at
			 * the master. So, let's read next.
			 */

		/* Read the ACK byte received */
		SPI_RxBlocking(SPI2, &ackByte, 1);

		if (SPI_VerifyResponse(ackByte))
		{
			/* Compose arguments */
			args[0] = LED_PIN;
			args[1] = LED_ON;

			/* Send arguments */
			SPI_TxBlocking(SPI2, args, 2);

			printf("CMD_LED_CTRL executed\n");
		}
		/* End of CMD_LED_CTRL */

		/* 2. CMD_SENSOR_READ <analog pin number(1)> -------------------------*/

		/* Wait until button is pressed */
		while (!GPIO_ReadFromInputPin(GPIOA, GPIO_PIN_0));

		/* Introduce debouncing time for button press */
		delay();

		cmdCode = CMD_SENSOR_READ;

		/* Send command */
		SPI_TxBlocking(SPI2, &cmdCode, 1);
			/* Remember! In SPI communication, when master or slave sends 1 byte of data
			 * it also receives 1 byte in return.
			 *
			 * This transmission of 1 byte results in 1 garbage byte collection in
			 * Rx buffer of the master and therefore RXNE flag will be set. So, do the
			 * dummy read and clear the flag.
			 */

		/* Dummy read to clear the RXNE bit */
		SPI_RxBlocking(SPI2, &dummyRead, 1);

		/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
		 * (To init the communication)
		 */
		SPI_TxBlocking(SPI2, &dummyWrite, 1);
			/* When this API call returns, response from the slave would've arrived at
			 * the master. So, let's read next.
			 */

		/* Read the ACK byte received */
		SPI_RxBlocking(SPI2, &ackByte, 1);

		if (SPI_VerifyResponse(ackByte))
		{
			/* Compose arguments */
			args[0] = ANALOG_PIN0;

			/* Send arguments */
			SPI_TxBlocking(SPI2, args, 1);


			/* Dummy read to clear the RXNE bit */
			SPI_RxBlocking(SPI2, &dummyRead, 1);

			/* Introduce delay to give slave enough time to do ADC conversion
			 * (Master should wait before generating the dummy bits to fetch
			 * the result.)
			 */
			delay();

			/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
			 * (To init the communication)
			 */
			SPI_TxBlocking(SPI2, &dummyWrite, 1);
				/* When this API call returns, response from the slave would've arrived at
				 * the master. So, let's read next.
				 */

			/* Read sensor data */
			uint8_t analogData;
			SPI_RxBlocking(SPI2, &analogData, 1);
				/* Analog data ranges from 0(0V) to 255(5V) */

			printf("CMD_SENSOR_READ: %d\n", analogData);
		}
		/* End of CMD_SENSOR_READ */

		/* 3. CMD_LED_READ <pin no(1)> ---------------------------------------*/

		/* Wait until button is pressed */
		while (!GPIO_ReadFromInputPin(GPIOA, GPIO_PIN_0));

		/* Introduce debouncing time for button press */
		delay();

		cmdCode = CMD_LED_READ;

		/* Send command */
		SPI_TxBlocking(SPI2, &cmdCode, 1);
			/* Remember! In SPI communication, when master or slave sends 1 byte of data
			 * it also receives 1 byte in return.
			 *
			 * This transmission of 1 byte results in 1 garbage byte collection in
			 * Rx buffer of the master and therefore RXNE flag will be set. So, do the
			 * dummy read and clear the flag.
			 */

		/* Dummy read to clear the RXNE bit */
		SPI_RxBlocking(SPI2, &dummyRead, 1);

		/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
		 * (To init the communication)
		 */
		SPI_TxBlocking(SPI2, &dummyWrite, 1);
			/* When this API call returns, response from the slave would've arrived at
			 * the master. So, let's read next.
			 */

		/* Read the ACK byte received */
		SPI_RxBlocking(SPI2, &ackByte, 1);

		if (SPI_VerifyResponse(ackByte))
		{
			/* Compose arguments */
			args[0] = LED_PIN;

			/* Send arguments */
			SPI_TxBlocking(SPI2, args, 1);


			/* Dummy read to clear the RXNE bit */
			SPI_RxBlocking(SPI2, &dummyRead, 1);

			/* Introduce delay to give slave enough time to do ADC conversion
			 * (Master should wait before generating the dummy bits to fetch
			 * the result.)
			 */
			delay();

			/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
			 * (To init the communication)
			 */
			SPI_TxBlocking(SPI2, &dummyWrite, 1);
				/* When this API call returns, response from the slave would've arrived at
				 * the master. So, let's read next.
				 */

			uint8_t ledStatus;
			SPI_RxBlocking(SPI2, &ledStatus, 1);

			printf("CMD_LED_READ: %d\n", ledStatus);
		}
		/* End of CMD_LED_READ */

		/* 4. CMD_PRINT <len(2)> <message(len)> ------------------------------*/

		/* Wait until button is pressed */
		while (!GPIO_ReadFromInputPin(GPIOA, GPIO_PIN_0));

		/* Introduce debouncing time for button press */
		delay();

		cmdCode = CMD_PRINT;

		/* Send command */
		SPI_TxBlocking(SPI2, &cmdCode, 1);
			/* Remember! In SPI communication, when master or slave sends 1 byte of data
			 * it also receives 1 byte in return.
			 *
			 * This transmission of 1 byte results in 1 garbage byte collection in
			 * Rx buffer of the master and therefore RXNE flag will be set. So, do the
			 * dummy read and clear the flag.
			 */

		/* Dummy read to clear the RXNE bit */
		SPI_RxBlocking(SPI2, &dummyRead, 1);

		/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
		 * (To init the communication)
		 */
		SPI_TxBlocking(SPI2, &dummyWrite, 1);
			/* When this API call returns, response from the slave would've arrived at
			 * the master. So, let's read next.
			 */

		/* Read the ACK byte received */
		SPI_RxBlocking(SPI2, &ackByte, 1);

		uint8_t message[] = "Hello, how are you?";
		if (SPI_VerifyResponse(ackByte))
		{
			/* Compose arguments */
			args[0] = strlen((char *)message);

			/* Send arguments */
			SPI_TxBlocking(SPI2, args, 1);	/* Sending length */

			/* Dummy read to clear the RXNE bit */
			SPI_RxBlocking(SPI2, &dummyRead, 1);

			/* Introduce delay to give slave enough time to do ADC conversion
			 * (Master should wait before generating the dummy bits to fetch
			 * the result.)
			 */
			delay();

			/* Send message */
			for (int i = 0; i < args[0]; i++)
			{
				SPI_TxBlocking(SPI2, &message[i], 1);
				SPI_RxBlocking(SPI2, &dummyRead, 1);
			}

			printf("CMD_PRINT executed\n");
		}
		/* End of CMD_PRINT */

		/* 5. CMD_ID_READ ----------------------------------------------------*/

		/* Wait until button is pressed */
		while (!GPIO_ReadFromInputPin(GPIOA, GPIO_PIN_0));

		/* Introduce debouncing time for button press */
		delay();

		cmdCode = CMD_PRINT;

		/* Send command */
		SPI_TxBlocking(SPI2, &cmdCode, 1);
			/* Remember! In SPI communication, when master or slave sends 1 byte of data
			 * it also receives 1 byte in return.
			 *
			 * This transmission of 1 byte results in 1 garbage byte collection in
			 * Rx buffer of the master and therefore RXNE flag will be set. So, do the
			 * dummy read and clear the flag.
			 */

		/* Dummy read to clear the RXNE bit */
		SPI_RxBlocking(SPI2, &dummyRead, 1);

		/* Send a dummy byte (or 2 bytes if 16-bit DFF) to fetch the response from the slave.
		 * (To init the communication)
		 */
		SPI_TxBlocking(SPI2, &dummyWrite, 1);
			/* When this API call returns, response from the slave would've arrived at
			 * the master. So, let's read next.
			 */

		/* Read the ACK byte received */
		SPI_RxBlocking(SPI2, &ackByte, 1);

		uint8_t id[11];
		uint32_t i = 0;
		if (SPI_VerifyResponse(ackByte))
		{
			/* Read 10 bytes ID from the slave */
			for (i = 0; i < 10; i++)
			{
				/* Send dummy byte to fetch data from slave */
				SPI_TxBlocking(SPI2, &dummyWrite, 1);
				SPI_RxBlocking(SPI2, &id[i], 1);
			}

			id[10] = '\0';

			printf("CMD_ID: %s\n", id);
		}
		/* End of CMD_ID_READ */

		/* Wait until SPI no longer busy */
		while (SPI2->SR & (0x1 << SPI_SR_BSY));
			/* SPI_SR bit[7] - BSY (Busy flag)
			 * 0: SPI (or I2S) not busy
			 * 1: SPI (or I2S) is busy in communication or Tx buffer is not empty
			 * This flag is set and cleared by hardware.
			 */

		/* Disable SPI2 peripheral (Terminate communication) */
		SPI_PeriControl(SPI2, DISABLE);

		printf("SPI communication closed.\n");
	}

	return 0;
} /* End of main */
```

> The master's clock frequency has been adjusted from 2 MHz (prescalar = 8) to 500 KHz (prescalar = 32) to be compatible with the baudrate (1200) of the slave.



## Arduino Sketch (`002SPISlaveCmdHandling.ino`)

```c
/*

 * SPI pin numbers:
 * SCK   13  // Serial Clock.
 * MISO  12  // Master In Slave Out.
 * MOSI  11  // Master Out Slave In.
 * SS    10  // Slave Select
 *

 */

#include <SPI.h>

const byte led = 9;           // Slave LED digital I/O pin.
boolean ledState = HIGH;      // LED state flag.
uint8_t dataBuff[255];
uint8_t board_id[10] = "ARDUINOUNO";

#define NACK 0xA5
#define ACK 0xF5

//command codes
#define COMMAND_LED_CTRL          0x50
#define COMMAND_SENSOR_READ       0x51
#define COMMAND_LED_READ          0x52
#define COMMAND_PRINT           0x53
#define COMMAND_ID_READ         0x54

#define LED_ON     1
#define LED_OFF    0

//arduino analog pins
#define ANALOG_PIN0   0
#define ANALOG_PIN1   1
#define ANALOG_PIN2   2
#define ANALOG_PIN3   3
#define ANALOG_PIN4   4

//Initialize SPI slave.
void SPI_SlaveInit(void) 
{ 
  // Initialize SPI pins.
  pinMode(SCK, INPUT);
  pinMode(MOSI, INPUT);
  pinMode(MISO, OUTPUT);
  pinMode(SS, INPUT);
  //make SPI as slave
  
  // Enable SPI as slave.
  SPCR = (1 << SPE);
}

//This function returns SPDR Contents 
uint8_t SPI_SlaveReceive(void)
{
  /* Wait for reception complete */
  while(!(SPSR & (1<<SPIF)));
  /* Return Data Register */
  return SPDR;
}


//sends one byte of data 
void SPI_SlaveTransmit(uint8_t data)
{
  /* Start transmission */
  SPDR = data;
  
  /* Wait for transmission complete */
  while(!(SPSR & (1<<SPIF)));
}
  

// The setup() function runs after reset.
void setup() 
{
  // Initialize serial for troubleshooting.
  Serial.begin(1200);
  
  // Initialize slave LED pin.
  pinMode(led, OUTPUT);
  
  digitalWrite(led,LOW);
  
  // Initialize SPI Slave.
  SPI_SlaveInit();
  
  Serial.println("Slave Initialized");
}


byte checkData(byte commnad)
{
  //todo
  return ACK;
}

// The loop function runs continuously after setup().
void loop() 
{
  byte data,command,len,ackornack=NACK;
  
  //1. fist make sure that ss is low . so lets wait until ss is low 
  Serial.println("Slave waiting for ss to go low");
  while(digitalRead(SS) );
  
  //2. now lets wait until rx buffer has a byte
  command = SPI_SlaveReceive();
  ackornack = checkData(command);
  
  SPI_SlaveTransmit(ackornack);
  
  len = SPI_SlaveReceive(); //dummy byte
  
  if(command == COMMAND_LED_CTRL)
  {
    //read 2 more bytes pin number and value 
    uint8_t pin = SPI_SlaveReceive(); 
    uint8_t value = SPI_SlaveReceive(); 
    Serial.println("RCVD:COMMAND_LED_CTRL");
    if(value == (uint8_t)LED_ON)
    {
      digitalWrite(pin,HIGH);
    }else if (value == (uint8_t) LED_OFF)
    {
      digitalWrite(pin,LOW);
    }
  
  }else if ( command == COMMAND_SENSOR_READ)
  {
    //read analog pin number 
    uint16_t aread;
    uint8_t pin = SPI_SlaveReceive(); 
    //pinMode(pin+14, INPUT_PULLUP);
    uint8_t val;
    aread = analogRead(pin+14);
    val = map(aread, 0, 1023, 0, 255);
    
    SPI_SlaveTransmit(val);
  
    val = SPI_SlaveReceive(); //dummy read
    
    Serial.println("RCVD:COMMAND_SENSOR_READ");
  
    
  
  }else if ( command == COMMAND_LED_READ)
  {
    uint8_t pin = SPI_SlaveReceive(); 
    uint8_t val = digitalRead(pin);
    SPI_SlaveTransmit(val);
    val = SPI_SlaveReceive(); //dummy read
    Serial.println("RCVD:COMMAND_LED_READ");
  
  }else if ( command == COMMAND_PRINT)
  {
    uint8_t len = SPI_SlaveReceive(); 
    for(int i=0 ; i < len ; i++)
    {
      dataBuff[i] = SPI_SlaveReceive();
    }
    Serial.println((char*)dataBuff);
    
    Serial.println("RCVD:COMMAND_PRINT");
  
  }else if ( command == COMMAND_ID_READ)
  {
    for(int i=0 ; i < strlen(board_id) ; i++)
    {
      SPI_SlaveTransmit(board_id[i]);
    }
      SPI_SlaveReceive();
    Serial.println("RCVD:COMMAND_ID_READ");
  }
 

}
```

> Original baudrate (9600) has been changed to 1200 since the communication didn't work with the original baudrate. (In the STM32 application, the master's clock frequency has been adjusted from 2 MHz to 500 KHz accordingly.)



## Testing

디버깅 필요! 디버거 모드로 한 줄 한 줄 실행 시 양쪽 콘솔에서 메시지들 정상 출력. 그냥 run 시 ackByte에 slave의 ACK 값이 정상적으로 저장되지 않아 일부 항목 건너 뛰는 문제가 있음.
