[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">MCU Peripheral Drivers</a> > GPIO Application 1: Toggling On-board LED (`gpio_01_led_toggle_pushpull.c`, `gpio_01_led_toggle_opendrain.c`)

# GPIO Application 1: Toggling On-board LED (`gpio_01_led_toggle_pushpull.c`, `gpio_01_led_toggle_opendrain.c`)



## Requirements

* Write a program to toggle the on-board LED with some delay.
  * Case 1: Use push pull configuration for the output pin
  * Case 2: Use open drain configuration of the output pin



## Code

### Case 1: `gpio_01_led_toggle_pushpull.c`

Path: `Project/Src/`

```c
/*******************************************************************************
 * File		: gpio_01_led_toggle_pushpull.c
 * Brief	: Program to toggle the on-board LED
 			  (Push-pull config for output pin)
 * Author	: Kyungjae Lee
 * Date		: May 23, 2023
 ******************************************************************************/

#include "stm32f407xx.h"

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
} /* End of delay */

int main(int argc, char *argv[])
{
	GPIO_Handle_TypeDef GPIOLed;

	GPIOLed.pGPIOx = GPIOD;
	GPIOLed.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_12;
	GPIOLed.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_OUT;
	GPIOLed.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH;
	GPIOLed.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_PP;
	GPIOLed.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;	/* Push-pull, no pupd necessary */

	GPIO_Init(&GPIOLed);

	while (1)
	{
		GPIO_ToggleOutputPin(GPIOLed.pGPIOx, GPIOLed.GPIO_PinConfig.GPIO_PinNumber);
		delay();
	}

	return 0;
} /* End of main */
```



### Case 2: `gpio_01_led_toggle_opendrain.c`

Path: `Project/Src/`

```c
/*******************************************************************************
 * File		: gpio_01_led_toggle_opendrain.c
 * Brief	: Program to toggle the on-board LED
 * 			  (Open-drain config for output pin)
 * Author	: Kyungjae Lee
 * Date		: May 23, 2023
 ******************************************************************************/

#include "stm32f407xx.h"

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
} /* End of delay */


int main(int argc, char *argv[])
{
	GPIO_Handle_TypeDef GPIOLed;

	GPIOLed.pGPIOx = GPIOD;
	GPIOLed.GPIO_PinConfig.GPIO_PinNumber = GPIO_PIN_12;
	GPIOLed.GPIO_PinConfig.GPIO_PinMode = GPIO_PIN_MODE_OUT;
	GPIOLed.GPIO_PinConfig.GPIO_PinSpeed = GPIO_PIN_OUT_SPEED_HIGH;
	GPIOLed.GPIO_PinConfig.GPIO_PinOutType = GPIO_PIN_OUT_TYPE_OD;	/* Open-drain config */
	GPIOLed.GPIO_PinConfig.GPIO_PinPuPdControl = GPIO_PIN_NO_PUPD;
		/* Even if you use internal pull-up resistor (GPIO_PIN_PU), the LED will toggle very dim.
		 * It is because of the high resistance of the internal pull-up resistor. Instead of
		 * using the internal pull-up resistor, add an external 470 ohm resistor with a
		 * jumper wire between PD12 and +5V. Then, the LED will toggle with normal brightness.
		 *
		 * For these reasons, it is not a good idea to use open-drain configuration for
		 * a GPIO output pin in general unless it is required by the requirements.
		 */

	GPIO_Init(&GPIOLed);

	while (1)
	{
		GPIO_ToggleOutputPin(GPIOLed.pGPIOx, GPIOLed.GPIO_PinConfig.GPIO_PinNumber);
		delay();
	}

	return 0;
} /* End of main */
```

