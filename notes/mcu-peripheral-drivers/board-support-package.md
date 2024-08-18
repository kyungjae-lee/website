[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">MCU Peripheral Drivers</a> > Board Support Package (BSP) (`rtc_ds1307.h/.c`, `lcd_hd44780u.h/.c`)

# Board Support Package (BSP) (`rtc_ds1307.h/.c`, `lcd_hd44780u.h/.c`)



## `rtc_ds1307.h`

* Contains device (RTC) related information
  * I2C address (For this project, slave address)
  * Register addresses (Registers from which the date and time information can be extracted)
  * Data structure to handle information
  * Prototypes of the functions that are to be exposed to user applications
  * Application configurable items

```c
/*******************************************************************************
 * File		: rtc_ds1307.h
 * Brief	: APIs for the DS1307 RTC module
 * Author	: Kyungjae Lee
 * Date		: Jun 25, 2023
 *
 * Note		: This code includes only the features that are necessary for my
 * 			  personal projects.
 ******************************************************************************/

#ifndef RTC_DS1307_H
#define RTC_DS1307_H

#include "stm32f407xx.h"

/* Application configurable items */
#define DS1307_I2C				I2C1
#define DS1307_I2C_GPIO_PORT	GPIOB
#define DS1307_I2C_PIN_SCL		GPIO_PIN_6
#define DS1307_I2C_PIN_SDA		GPIO_PIN_7
#define DS1307_I2C_SPEED		I2C_SCL_SPEED_SM /* Doesn't support fast mode */
#define DS1307_I2C_PUPD			GPIO_PIN_PU		 /* Using internal pull-up R */

/* Register addresses */
#define DS1307_SEC				0x00
#define DS1307_MIN				0x01
#define DS1307_HR				0x02
#define DS1307_DAY				0x03
#define DS1307_DATE  			0x04
#define DS1307_MONTH			0x05
#define DS1307_YEAR				0x06

/* DS1307 I2C address */
#define DS1307_I2C_ADDR			0x68 /* 1101000(2) */

/* Time formats */
#define TIME_FORMAT_12HRS_AM 	0
#define TIME_FORMAT_12HRS_PM	1
#define TIME_FORMAT_24HRS	 	2

/* Days */
#define SUNDAY					0
#define MONDAY					1
#define TUESDAY					2
#define WEDNESDAY				3
#define THURSDAY				4
#define FRIDAY					5
#define SATURDAY				6

/* RTC date configuration structure */
typedef struct
{
	uint8_t date;
	uint8_t month;
	uint8_t year;
	uint8_t day;
} RTC_Date_TypeDef;

/* RTC time configuration structure */
typedef struct
{
	uint8_t seconds;
	uint8_t minutes;
	uint8_t hours;
	uint8_t	timeFormat;
} RTC_Time_TypeDef;


/*******************************************************************************
 * APIs supported by the DS1307 RTC module
 * (See the function definitions for more information)
 ******************************************************************************/

uint8_t DS1307_Init(void);
void DS1307_SetCurrentTime(RTC_Time_TypeDef *);
void DS1307_GetCurrentTime(RTC_Time_TypeDef *);
void DS1307_SetCurrentDate(RTC_Date_TypeDef *);
void DS1307_GetCurrentDate(RTC_Date_TypeDef *);


#endif /* RTC_DS1307_H */
```



## `rtc_ds1307.c`

```c
/*******************************************************************************
 * File		: rtc_ds1307.c
 * Brief	: Implementation of APIs for the DS1307 RTC module
 * Author	: Kyungjae Lee
 * Date		: Jun 25, 2023
 *
 * Note		: This code includes only the features that are necessary for my
 * 			  personal projects.
 ******************************************************************************/

#include <rtc_ds1307.h>
#include <string.h> 	/* memset() */
#include <stdint.h>

/* Private function prototypes */
static void DS1307_I2CPinConfig(void);
static void DS1307_I2CConfig(void);
static void DS1307_Write(uint8_t value, uint8_t regAddr);
static uint8_t DS1307_Read(uint8_t regAddr);
static uint8_t BcdToBinary(uint8_t bcd);
static uint8_t BinaryToBcd(uint8_t binary);

/* Global variables */
I2C_Handle_TypeDef gDS1307I2CHandle;

/**
 * DS1307_Init()
 * Desc.	: Initializes DS1307 RTC module
 * Param.	: None
 * Return	: 0 if setting DS1307_SEC CH bit to 0 was successful (init success),
 * 			  1 otherwise (init fail)
 * Note		: N/A
 */
uint8_t DS1307_Init(void)
{
	/* 1. Initialize the I2C pins */
	DS1307_I2CPinConfig();

	/* 2. Initialize the I2C peripheral */
	DS1307_I2CConfig();

	/* 3. Enable the I2C peripheral */
	I2C_PeriControl(DS1307_I2C, ENABLE);

	/* 4. Set Clock Halt (CH) bit to 0 to initiate time keeping
	 * Note: At power on, the time keeping will not function until the CH bit
	 * 		 becomes 0. So, to initiate the time keeping functionality the
  	 *		 master needs to write 0 to the slave's CH bit.
  	 *		 For data write logic, see the "I2C Data Bus" section of DS1307
  	 *		 reference manual.
  	 */
	DS1307_Write(0x0, DS1307_SEC);

	/* 5. Read back Clock Halt (CH) bit
	 * Note: For data read logic, see the "I2C Data Bus" section of DS1307
	 * 		 reference manual.
	 */
	uint8_t clockState = DS1307_Read(DS1307_SEC);

	return ((clockState >> 7) & 0x1);
}

/**
 * DS1307_SetCurrentTime()
 * Desc.	: Sets the DS1307 Seconds, Minutes, Hours registers according to
 * 			  the values configured in @rtcTime
 * Param.	: @rtcTime - pointer to the RTC Time structure which contains the
 * 			  values configured by the user
 * Return	: None
 * Note		: N/A
 */
void DS1307_SetCurrentTime(RTC_Time_TypeDef *rtcTime)
{
	uint8_t secs, hrs;

	/* Set seconds -----------------------------------------------------------*/

	secs = BinaryToBcd(rtcTime->seconds);

	/* Make sure to keep the CH bit (bit[7]) of the Seconds register */
	secs &= ~(0x1 << 7);

	DS1307_Write(secs, DS1307_SEC);

	/* Set minutes -----------------------------------------------------------*/

	DS1307_Write(BinaryToBcd(rtcTime->minutes), DS1307_MIN);

	/* Set hours -------------------------------------------------------------*/

	hrs = BinaryToBcd(rtcTime->hours);

	if (rtcTime->timeFormat == TIME_FORMAT_24HRS)
	{
		/* To use 24HRS time format, clear bit[6] of the Hours register */
		hrs &= ~(0x1 << 6);
	}
	else
	{
		/* To use 12HRS time format, set bit[6] of the Hours register */
		hrs |= (0x1 << 6);

		/* If PM, set bit[5] of the Hours register, if AM, clear it */
		hrs = (rtcTime->timeFormat == TIME_FORMAT_12HRS_PM) ? hrs | (0x1 << 5) : hrs & ~(0x1 << 5);
	}

	DS1307_Write(hrs, DS1307_HR);
} /* End of DS1307_SetCurrentTime */

/**
 * DS1307_GetCurrentTime()
 * Desc.	: Gets the current time information and stores it into @rtcTime
 * Param.	: @rtcTime - RTC_Time structure to store the current time info
 * Return	: None
 * Note		: N/A
 */
void DS1307_GetCurrentTime(RTC_Time_TypeDef *rtcTime)
{
	uint8_t secs, hrs;

	/* Get seconds -----------------------------------------------------------*/

	secs = DS1307_Read(DS1307_SEC);
	secs &= ~(0x1 << 7); 	/* Exclude unnecessary bits */
	rtcTime->seconds = BcdToBinary(secs);

	/* Get minutes -----------------------------------------------------------*/

	rtcTime->minutes = BcdToBinary(DS1307_Read(DS1307_MIN));

	/* Get hours -------------------------------------------------------------*/

	hrs = DS1307_Read(DS1307_HR);

	if (hrs & (0x1 << 6))
	{
		/* 12HRS format */
		rtcTime->timeFormat = !((hrs & (0x1 << 5)) == 0);
		hrs &= ~(0x3 << 5);	/* Clear bit[6] and bit[5] */
	}
	else
	{
		/* 24HRS format */
		rtcTime->timeFormat = TIME_FORMAT_24HRS;
	}

	rtcTime->hours = BcdToBinary(hrs);
} /* End of DS1307_GetCurrentTime */

/**
 * DS1307_SetCurrentDate()
 * Desc.	: Sets the current date information into @rtcDate
 * Param.	: @rtcDate - RTC_Date structure which contains the current date info
 * Return	: None
 * Note		: N/A
 */
void DS1307_SetCurrentDate(RTC_Date_TypeDef *rtcDate)
{
	DS1307_Write(BinaryToBcd(rtcDate->date), DS1307_DATE);
	DS1307_Write(BinaryToBcd(rtcDate->month), DS1307_MONTH);
	DS1307_Write(BinaryToBcd(rtcDate->year), DS1307_YEAR);
	DS1307_Write(BinaryToBcd(rtcDate->day), DS1307_DAY);
} /* End of DS1307_SetCurrentDate */

/**
 * DS1307_GetCurrentDate()
 * Desc.	: Gets the current date information and stores it into @rtcDate
 * Param.	: @rtcDate - RTC_Date structure to store the current date info
 * Return	: None
 * Note		: N/A
 */
void DS1307_GetCurrentDate(RTC_Date_TypeDef *rtcDate)
{
	rtcDate->day = BcdToBinary(DS1307_Read(DS1307_DAY));
	rtcDate->date = BcdToBinary(DS1307_Read(DS1307_DATE));
	rtcDate->month = BcdToBinary(DS1307_Read(DS1307_MONTH));
	rtcDate->year = BcdToBinary(DS1307_Read(DS1307_YEAR));
} /* End of DS1307_GetCurrentDate */


/*******************************************************************************
 * Private functions
 ******************************************************************************/

/**
 * DS1307_I2CPinConfig()
 * Desc.	: Configures the GPIO pins to be used for I2C communication
 * Param.	: None
 * Return	: None
 * Note		: N/A
 */
static void DS1307_I2CPinConfig(void)
{
	GPIO_Handle_TypeDef i2cSda, i2cScl;

	/* Zero-out all the fields in the structures (Very important! i2cSda, i2cScl
	 * are local variables whose members may be filled with garbage values before
	 * initialization. These garbage values may set (corrupt) the bit fields that
	 * you did not touch assuming that they will be 0 by default. Do NOT make this
	 * mistake!
	 */
	memset(&i2cSda, 0, sizeof(i2cSda));
	memset(&i2cScl, 0, sizeof(i2cScl));

	/*
	 * I2C1_SCL: PB6
	 * I2C1_SDA: PB7
	 */

	i2cSda.pGPIOx = DS1307_I2C_GPIO_PORT;
	i2cSda.GPIO_PinConfig.GPIO_PinAltFcnMode = 4;
	i2cSda.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_ALTFCN;
	i2cSda.GPIO_PinConfig.GPIO_PinNumber = DS1307_I2C_PIN_SDA;
	i2cSda.GPIO_PinConfig.GPIO_PinOutType= GPIO_PIN_OUT_TYPE_OD;
	i2cSda.GPIO_PinConfig.GPIO_PinPuPdControl = DS1307_I2C_PUPD;
	i2cSda.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH;

	GPIO_Init(&i2cSda);

	i2cScl.pGPIOx = DS1307_I2C_GPIO_PORT;
	i2cScl.GPIO_PinConfig.GPIO_PinAltFcnMode = 4;
	i2cScl.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_ALTFCN;
	i2cScl.GPIO_PinConfig.GPIO_PinNumber = DS1307_I2C_PIN_SCL;
	i2cScl.GPIO_PinConfig.GPIO_PinOutType= GPIO_PIN_OUT_TYPE_OD;
	i2cScl.GPIO_PinConfig.GPIO_PinPuPdControl = DS1307_I2C_PUPD;
	i2cScl.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH;

	GPIO_Init(&i2cScl);
} /* End of DS1307_I2CPinConfig */

/**
 * DS1307_I2CConfig()
 * Desc.	: Configures and initializes DS1307 I2C peripheral
 * Param.	: None
 * Return	: None
 * Note		: N/A
 */
static void DS1307_I2CConfig(void)
{
	gDS1307I2CHandle.pI2Cx = DS1307_I2C;
	gDS1307I2CHandle.I2C_Config.I2C_ACKEnable = I2C_ACK_ENABLE;
	gDS1307I2CHandle.I2C_Config.I2C_SCLSpeed = DS1307_I2C_SPEED;
	I2C_Init(&gDS1307I2CHandle);
} /* End of DS1307_I2CConfig */

/**
 * DS1307_Write()
 * Desc.	: Writes @value to @regAddr
 * Param.	: @value - value to write to @regAddr
 *            @regAddr - DS1307 register address to write @value to
 * Return	: None
 * Note		: N/A
 */
static void DS1307_Write(uint8_t value, uint8_t regAddr)
{
	uint8_t tx[2];
	tx[0] = regAddr;
	tx[1] = value;
	I2C_MasterTxBlocking(&gDS1307I2CHandle, tx, 2, DS1307_I2C_ADDR, 0);

} /* End of DS1307_Write */

/**
 * DS1307_Read()
 * Desc.	: Writes @value to @regAddr
 * Param.   : @regAddr - DS1307 register address to read from
 * Return	: 1-byte data received from the slave (DS1307 RTC module)
 * Note		: N/A
 */
static uint8_t DS1307_Read(uint8_t regAddr)
{
	uint8_t rxData;
	I2C_MasterTxBlocking(&gDS1307I2CHandle, &regAddr, 1, DS1307_I2C_ADDR, 0);
	I2C_MasterRxBlocking(&gDS1307I2CHandle, &rxData, 1, DS1307_I2C_ADDR, 0);

	return rxData;
} /* End of DS1307_Read */

/**
 * BcdToBinary()
 * Desc.	: Converts the passed Binary-Coded Decimal (BCD) value to binary
 * Param.   : @bcd - binary-coded decimal value to be converted into binary
 * Return	: @bcd in binary representation
 * Note		: N/A
 */
static uint8_t BcdToBinary(uint8_t bcd)
{
	uint8_t tens, ones;

	tens = (uint8_t)((bcd >> 4) * 10);
	ones = bcd & (uint8_t)0xF;

	return tens + ones;
} /* End of BcdToBinary */

/**
 * BinaryToBcd()
 * Desc.	: Converts the passed binary value to Binary-Coded Decimal (BCD)
 * Param.   : @binary - binary value to be converted into binary-coded decimal
 * Return	: @binary in binary-coded decimal representation
 * Note		: N/A
 */
uint8_t BinaryToBcd(uint8_t binary)
{
	uint8_t tens, ones;
	uint8_t bcd;

	bcd = binary;

	if (binary >= 10)
	{
		tens = binary / 10;
		ones = binary % 10;
		bcd = (tens << 4) | ones;
	}

	return bcd;
} /* End of BinaryToBcd */
```



## `lcd_hd44780u.h`

```c
/*******************************************************************************
 * File		: lcd_hd44780u.c
 * Brief	: Implementation of APIs for the HD44780U 16x2 Character LCD module
 * 			  (4-bit interface; only DB4-7 pins of the LCD module will be used)
 * Author	: Kyungjae Lee
 * Date		: Jun 27, 2023
 *
 * Note		: This code includes only the features that are necessary for my
 * 			  personal projects.
 ******************************************************************************/

#include "lcd_hd44780u.h"
#include "stm32f407xx.h"

/* Function prototypes */
static void Write4Bits(uint8_t nibble);
static void LCD_Enable(void);
static void DelayMs(uint32_t delayInMs);
static void DelayUs(uint32_t delayInUs);

/**
 * LCD_Init()
 * Desc.	: Initializes GPIO pins to be used for LCD connections
 * Param.	: None
 * Return	: None
 * Note		: N/A
 */
void LCD_Init(void)
{
	GPIO_Handle_TypeDef gpioPin;

	/* 1. Configure the GPIO pins to be used for LCD connections -------------*/

	/* Configure the GPIO pin to be connected to LCD RS pin */
	gpioPin.pGPIOx = LCD_GPIO_PORT;
	gpioPin.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_OUT;
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_RS;
	gpioPin.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_PP;
	gpioPin.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;
	gpioPin.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD RW pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_RW;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD EN pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_EN;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D4 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D4;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D5 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D5;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D6 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D6;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D7 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D7;
	GPIO_Init(&gpioPin);

	/* Set the configured GPIO pins' output values to 0 */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_EN, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D4, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D5, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D6, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D7, RESET);

	/* 2. Initialize LCD module ------------------------------------------------
	 * (See 'Figure 24. 4-Bit Interface' in the LCD reference manual)
	 */

	/* Wait for more than 40 ms after Vcc rises to 2.7V */
	DelayMs(40);

	/* Set RS pin to 0 for LCD command */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, RESET);

	/* Set RW pin to 0 for writing to LCD (If you are always writing to LCD, you
	 * can keep this pin connected to GND)
	 */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);

	Write4Bits(0x3);	/* 0 0 1 1 in D7 D6 D5 D4 order */

	DelayMs(5);

	Write4Bits(0x3);

	DelayUs(150);

	Write4Bits(0x3);
	Write4Bits(0x2);

	/* Now, ready to send the command */

	/* Set to use 4 data lines, 2 lines, 5x8 char font size */
	LCD_TxInstruction(LCD_INST_4DL_2N_5X8F);

	/* Set display on, cursor on */
	LCD_TxInstruction(LCD_INST_DON_CURON);

	/* Clear display */
	LCD_ClearDisplay();

	/* Entry mode set */
	LCD_TxInstruction(LCD_INST_INCADD);

} /* End of LCD_Init */

/**
 * LCD_TxInstruction()
 * Desc.	: Sends passed instruction (@instruction) to LCD
 * Param.	: @instruction - LCD instruction to send
 * Return	: None
 * Note		: N/A
 */
void LCD_TxInstruction(uint8_t instruction)
{
	/* 1. Set RS=0 for LCD command */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, RESET);

	/* 2. Set RW=0 for write */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);

	Write4Bits(instruction >> 4);	/* Send the higher nibble */
	Write4Bits(instruction & 0xF);	/* Send the lower nibble */
} /* End of LCD_TxInstruction */

/**
 * LCD_PrintChar()
 * Desc.	: Sends a character to be printed to the LCD
 * Param.	: @ch - character to print
 * Return	: None
 * Note		: This function assumes 4-bit parallel data transmission.
 * 			  First, the higher nibble of the data will be sent through the data
 * 			  lines D4, D5, d6, d7,
 * 			  then, the lower nibble of the data will be sent through the data
 * 			  lines D4, D5, d6, d7.
 */
void LCD_PrintChar(uint8_t ch)
{
	/* 1. Set RS=1 for LCD user data */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, SET);

	/* 2. Set RW=0 for write */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);

	Write4Bits(ch >> 4);	/* Send the higher nibble */
	Write4Bits(ch & 0xF);	/* Send the lower nibble */
} /* End of LCD_TxChar */

/**
 * LCD_ClearDisplay()
 * Desc.	: Performs display clear routine
 * Param.	: None
 * Return	: None
 * Note		: After sending the clear display instruction, give it 2 ms as per
 * 			  the datasheet of LCD (See p.24 of LCD datasheet).
 */
void LCD_ClearDisplay(void)
{
	/* Clear display */
	LCD_TxInstruction(LCD_INST_CLEAR_DISPLAY);

	DelayMs(2);	/* Wait 2 ms as per the datasheet */
} /* End of LCD_ClearDisplay */

/**
 * LCD_ReturnHome()
 * Desc.	: Returns the cursor to the home position
 * Param.	: None
 * Return	: None
 * Note		: After sending the clear display instruction, give it 2 ms as per
 * 			  the datasheet of LCD (See p.24 of LCD datasheet).
 */
void LCD_ReturnHome(void)
{
	/* Return home */
	LCD_TxInstruction(LCD_INST_RETURN_HOME);

	DelayMs(2);	/* Wait 2 ms as per the datasheet */
} /* End of LCD_ReturnHome */

/**
 * LCD_PrintString()
 * Desc.	: Prints the passed string @msg to the LCD screen
 * Param.	: @msg - string to print
 * Return	: None
 * Note		: N/A
 */
void LCD_PrintString(char *msg)
{
	do
	{
		LCD_PrintChar((uint8_t)*msg++);
	}
	while (*msg != '\0');
} /* End of LCD_PrintString */

/**
 * LCD_SetCursor()
 * Desc.	: Sets the cursor position (@row, @column)
 * Param.	: @row - the row in which the cursor should be placed
 * 			  @column - the column in which the cursor should be placed
 * Return	: None
 * Note		: Row number: 1 ~ 2
 * 			  Column number: 1 ~ 16 assuming a 2x16 character display
 */
void LCD_SetCursor(uint8_t row, uint8_t column)
{
	column--;

	switch (row)
	{
	case 1:
		/* Set cursor to 1st row address and add index */
		LCD_TxInstruction((column |= 0x80));
		break;
	case 2:
		/* Set cursor to 2nd row address and add index */
		LCD_TxInstruction((column |= 0xC0));
		break;
	default:
		break;
	}
} /* End of LCD_SetCursor */


/*******************************************************************************
 * Private functions
 ******************************************************************************/

/**
 * DelayMs()
 * Desc.	: Spinlock delays for @delayInMs milliseconds
 * Param.	: @delayInMs - time to delay in milliseconds
 * Returns	: None
 * Note		: N/A
 */
static void DelayMs(uint32_t delayInMs)
{
	for (uint32_t i = 0; i < delayInMs * 1000; i++);
} /* End of DelayMs */

/**
 * DelayUs()
 * Desc.	: Spinlock delays for @delayInUs microseconds
 * Param.	: @delayInUs - time to delay in microseconds
 * Returns	: None
 * Note		: N/A
 */
static void DelayUs(uint32_t delayInUs)
{
	for (uint32_t i = 0; i < delayInUs * 1; i++);
} /* End of DelayMs */

/**
 * Write4Bits()
 * Desc.	: Writes 4 bits of data/command to D4, D5, D6, D7 lines
 * 			  (msb to D7, lsb to D4)
 * Param.	: @value -
 * Return	: None
 * Note		: N/A
 */
static void Write4Bits(uint8_t nibble)
{
	/* Write each bit of @nibble to D4-D7 lines */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D4, (nibble >> 0) & 0x1);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D5, (nibble >> 1) & 0x1);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D6, (nibble >> 2) & 0x1);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D7, (nibble >> 3) & 0x1);

	/* After writing every nibble to the data lines, give the LCD time to latch
	 * that data inside the LCD
	 */
	/* Make high-to-low transition on EN pin (See the timing diagram) */
	LCD_Enable();

} /* End of Write4Bits */

/**
 * LCD_Enable()
 * Desc.	: Makes high-to-low transition on the EN line
 * Param.	: None
 * Return	: None
 * Note		: For the instruction execution time, give any value that is greater
 * 			  than 32 micro-seconds.
 */
static void LCD_Enable(void)
{
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_EN, SET);
	DelayUs(10);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_EN, RESET);
	DelayUs(100);
} /* End of LCD_Enable */
```



## `lcd_hd44780u.c`

```c
/*******************************************************************************
 * Filename		: lcd_hd44780u.c
 * Description	: Implementation of APIs for 16x2 Character LCD module
 * 				  (4-bit interface; only DB4-DB7 pins of the LCD module will be
 * 				  used)
 * Author		: Kyungjae Lee
 * History 		: Jun 27, 2023 - Created file
 ******************************************************************************/

#include "lcd_hd44780u.h"
#include "stm32f407xx.h"

/* Function prototypes */
static void Write4Bits(uint8_t nibble);
static void LCD_Enable(void);
static void DelayMs(uint32_t delayInMs);
static void DelayUs(uint32_t delayInUs);

/**
 * LCD_Init()
 * Desc.	: Initializes GPIO pins to be used for LCD connections
 * Param.	: None
 * Return	: None
 * Note		: N/A
 */
void LCD_Init(void)
{
	GPIO_Handle_TypeDef gpioPin;

	/* 1. Configure the GPIO pins to be used for LCD connections -------------*/

	/* Configure the GPIO pin to be connected to LCD RS pin */
	gpioPin.pGPIOx = LCD_GPIO_PORT;
	gpioPin.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_OUT;
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_RS;
	gpioPin.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_PP;
	gpioPin.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;
	gpioPin.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD RW pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_RW;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD EN pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_EN;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D4 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D4;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D5 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D5;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D6 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D6;
	GPIO_Init(&gpioPin);

	/* Configure the GPIO pin to be connected to LCD D7 pin */
	gpioPin.GPIO_PinConfig.GPIO_PinNumber = LCD_PIN_D7;
	GPIO_Init(&gpioPin);

	/* Set the configured GPIO pins' output values to 0 */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_EN, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D4, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D5, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D6, RESET);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D7, RESET);

	/* 2. Initialize LCD module ------------------------------------------------
	 * (See 'Figure 24. 4-Bit Interface' in the LCD reference manual)
	 */

	/* Wait for more than 40 ms after Vcc rises to 2.7V */
	DelayMs(40);

	/* Set RS pin to 0 for LCD command */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, RESET);

	/* Set RW pin to 0 for writing to LCD (If you are always writing to LCD, you
	 * can keep this pin connected to GND)
	 */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);

	Write4Bits(0x3);	/* 0 0 1 1 in D7 D6 D5 D4 order */

	DelayMs(5);

	Write4Bits(0x3);

	DelayUs(150);

	Write4Bits(0x3);
	Write4Bits(0x2);

	/* Now, ready to send the command */

	/* Set to use 4 data lines, 2 lines, 5x8 char font size */
	LCD_TxInstruction(LCD_INST_4DL_2N_5X8F);

	/* Set display on, cursor on */
	LCD_TxInstruction(LCD_INST_DON_CURON);

	/* Clear display */
	LCD_ClearDisplay();

	/* Entry mode set */
	LCD_TxInstruction(LCD_INST_INCADD);

} /* End of LCD_Init */

/**
 * LCD_TxInstruction()
 * Desc.	: Sends passed instruction (@instruction) to LCD
 * Param.	: @instruction - LCD instruction to send
 * Return	: None
 * Note		: N/A
 */
void LCD_TxInstruction(uint8_t instruction)
{
	/* 1. Set RS=0 for LCD command */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, RESET);

	/* 2. Set RW=0 for write */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);

	Write4Bits(instruction >> 4);	/* Send the higher nibble */
	Write4Bits(instruction & 0xF);	/* Send the lower nibble */
} /* End of LCD_TxInstruction */

/**
 * LCD_PrintChar()
 * Desc.	: Sends a character to be printed to the LCD
 * Param.	: @ch - character to print
 * Return	: None
 * Note		: This function assumes 4-bit parallel data transmission.
 * 			  First, the higher nibble of the data will be sent through the data
 * 			  lines D4, D5, d6, d7,
 * 			  then, the lower nibble of the data will be sent through the data
 * 			  lines D4, D5, d6, d7.
 */
void LCD_PrintChar(uint8_t ch)
{
	/* 1. Set RS=1 for LCD user data */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RS, SET);

	/* 2. Set RW=0 for write */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_RW, RESET);

	Write4Bits(ch >> 4);	/* Send the higher nibble */
	Write4Bits(ch & 0xF);	/* Send the lower nibble */
} /* End of LCD_TxChar */

/**
 * LCD_ClearDisplay()
 * Desc.	: Performs display clear routine
 * Param.	: None
 * Return	: None
 * Note		: After sending the clear display instruction, give it 2 ms as per
 * 			  the datasheet of LCD (See p.24 of LCD datasheet).
 */
void LCD_ClearDisplay(void)
{
	/* Clear display */
	LCD_TxInstruction(LCD_INST_CLEAR_DISPLAY);

	DelayMs(2);	/* Wait 2 ms as per the datasheet */
}

/**
 * LCD_ReturnHome()
 * Desc.	: Returns the cursor to the home position
 * Param.	: None
 * Return	: None
 * Note		: After sending the clear display instruction, give it 2 ms as per
 * 			  the datasheet of LCD (See p.24 of LCD datasheet).
 */
void LCD_ReturnHome(void)
{
	/* Return home */
	LCD_TxInstruction(LCD_INST_RETURN_HOME);

	DelayMs(2);	/* Wait 2 ms as per the datasheet */
} /* End of LCD_ReturnHome */

/**
 * LCD_PrintString()
 * Desc.	: Prints the passed string @msg to the LCD screen
 * Param.	: @msg - string to print
 * Return	: None
 * Note		: N/A
 */
void LCD_PrintString(char *msg)
{
	do
	{
		LCD_PrintChar((uint8_t)*msg++);
	}
	while (*msg != '\0');
} /* End of LCD_PrintString */

/**
 * LCD_SetCursor()
 * Desc.	: Sets the cursor position (@row, @column)
 * Param.	: @row - the row in which the cursor should be placed
 * 			  @column - the column in which the cursor should be placed
 * Return	: None
 * Note		: Row number: 1 ~ 2
 * 			  Column number: 1 ~ 16 assuming a 2x16 character display
 */
void LCD_SetCursor(uint8_t row, uint8_t column)
{
	column--;

	switch (row)
	{
	case 1:
		/* Set cursor to 1st row address and add index */
		LCD_TxInstruction((column |= 0x80));
		break;
	case 2:
		/* Set cursor to 2nd row address and add index */
		LCD_TxInstruction((column |= 0xC0));
		break;
	default:
		break;
	}
} /* End of LCD_SetCursor */



/*******************************************************************************
 * Private functions
 ******************************************************************************/

/**
 * DelayMs()
 * Desc.	: Spinlock delays for @delayInMs milliseconds
 * Param.	: @delayInMs - time to delay in milliseconds
 * Returns	: None
 * Note		: N/A
 */
static void DelayMs(uint32_t delayInMs)
{
	for (uint32_t i = 0; i < delayInMs * 1000; i++);
} /* End of DelayMs */

/**
 * DelayUs()
 * Desc.	: Spinlock delays for @delayInUs microseconds
 * Param.	: @delayInUs - time to delay in microseconds
 * Returns	: None
 * Note		: N/A
 */
static void DelayUs(uint32_t delayInUs)
{
	for (uint32_t i = 0; i < delayInUs * 1; i++);
} /* End of DelayMs */

/**
 * Write4Bits()
 * Desc.	: Writes 4 bits of data/command to D4, D5, D6, D7 lines
 * 			  (msb to D7, lsb to D4)
 * Param.	: @value -
 * Return	: None
 * Note		: N/A
 */
static void Write4Bits(uint8_t nibble)
{
	/* Write each bit of @nibble to D4-D7 lines */
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D4, (nibble >> 0) & 0x1);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D5, (nibble >> 1) & 0x1);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D6, (nibble >> 2) & 0x1);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_D7, (nibble >> 3) & 0x1);

	/* After writing every nibble to the data lines, give the LCD time to latch
	 * that data inside the LCD
	 */
	/* Make high-to-low transition on EN pin (See the timing diagram) */
	LCD_Enable();

} /* End of Write4Bits */

/**
 * LCD_Enable()
 * Desc.	: Makes high-to-low transition on the EN line
 * Param.	: None
 * Return	: None
 * Note		: For the instruction execution time, give any value that is greater
 * 			  than 32 micro-seconds.
 */
static void LCD_Enable(void)
{
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_EN, SET);
	DelayUs(10);
	GPIO_WriteToOutputPin(LCD_GPIO_PORT, LCD_PIN_EN, RESET);
	DelayUs(100);
} /* End of LCD_Enable */
```
