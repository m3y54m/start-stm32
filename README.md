# Getting Started with STM32 Programming

The BluePill STM32F103C8 board is used for this project.

## LED Blink Program

### [With No Libraries](blinky-no-lib) 

This program is based on the article ***["Bare Metal STM32 Programming – LED Blink"](https://freeelectron.ro/bare-metal-stm32-led-blink/)*** without using any external libraries (except `stdint.h` which is only used to define `uint32_t`).

In order to compile and link this program we need the main program source file [`main.c`](blinky-no-lib/src/main.c), the linker script file [`linker.ld`](blinky-no-lib/src/linker.ld), and the C run-time assembly file [`crt.s`](blinky-no-lib/src/crt.s).

```c
#include <stdint.h>

// register address
#define RCC_BASE      0x40021000
#define GPIOC_BASE    0x40011000

#define RCC_APB2ENR   *(volatile uint32_t *)(RCC_BASE   + 0x18)
#define GPIOC_CRH     *(volatile uint32_t *)(GPIOC_BASE + 0x04)
#define GPIOC_ODR     *(volatile uint32_t *)(GPIOC_BASE + 0x0C)

// bit fields
#define RCC_IOPCEN   (1<<4)
#define GPIOC13      (1UL<<13)

void main(void)
{
    RCC_APB2ENR |= RCC_IOPCEN;
    GPIOC_CRH   &= 0xFF0FFFFF;
    GPIOC_CRH   |= 0x00200000;

    while(1)
    {
        GPIOC_ODR |=  GPIOC13;
        for (int i = 0; i < 500000; i++); // arbitrary delay
        GPIOC_ODR &= ~GPIOC13;
        for (int i = 0; i < 500000; i++); // arbitrary delay
    }
}
```

### [Using STM32 HAL](blinky-hal)

This program is based on the article ***["STM32 LED Blink"](https://stm32world.com/wiki/STM32_LED_Blink)*** which uses STM32 HAL to configure the microcontroller and blink the LED.

The project initialization is done by STM32CubeMX.

```c
  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {

	// Toggle the LED
	HAL_GPIO_TogglePin(GPIOC, GPIO_PIN_13);

	// Wait for 500 ms
	HAL_Delay(500);

	// Rinse and repeat :)

    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
```

## Uploading

The best way to upload the sketch is to use ST-Link or J-Link debuggers or any other debugger supported by OpenOCD:

For example you can upload your program to STM32F103 microcontroller [using an FT232H module](https://github.com/m3y54m/cjmcu-ft232hq-programmer) and `openocd` tool using this command (`firmware.bin` is the name of your program binary file):

```bash
openocd -f interface/ftdi/ft232h-module-swd.cfg -f target/stm32f1x.cfg  -c init -c "reset halt" -c "flash write_image erase firmware.bin 0x08000000" -c "reset" -c shutdown
```

If you are using ST-LINK, you can use one of these two methods:

Using `st-flash` tool:

```bash
st-flash write firmware.bin 0x8000000
```
Using `openocd` tool:

```bash
openocd -f interface/stlink.cfg -f target/stm32f1x.cfg  -c init -c "reset halt" -c "flash write_image erase firmware.bin 0x08000000" -c "reset" -c shutdown
```

But due to the additional hardware costs you may want to use a cheaper alternative method.

I have tried to upload the program using the DFU, HID, and Maple bootloaders but I couldn't get them to work.

[STM32 Maple DFU Bootloader 2.0](https://github.com/rogerclarkmelbourne/STM32duino-bootloader/blob/master/binaries/generic_boot20_pc13.bin)

[STM32 HID Bootloader](https://github.com/Serasidis/STM32_HID_Bootloader/releases)

You can find some Arduino-STM32 upload methods in this page:

[Program STM32 Blue Pill (STM32F103C8T6) with Arduino IDE](https://www.sgbotic.com/index.php?dispatch=pages.view&page_id=48)

STM32F103C8T6 has an embedded bootloader in its ROM that can be used to upload the sketch. This is the best alternative method I've found.

This method is implemented in PlatformIO under the name `upload_protocol` of `serial`.

All the hardware you need is a connection between UART1 of the board (PA9-TX1 and PA10-RX1) and a USB to serial dongle connected to your computer.

![image](https://user-images.githubusercontent.com/1549028/213869831-610a84d2-9df3-4d2a-bf80-3e32a9a684b5.png)

> Before uploading the sketch, make sure that BOOT0 is connected to VCC and RESET button is pressed. This will start up the microcontroller in the bootloader mode.
 
> Each time you change the configuration of BOOT0, you should RESET the board.

![image](https://user-images.githubusercontent.com/1549028/213869836-5bc00653-19df-4bdc-853b-94aeb717bb58.png)

### Boot Modes

A couple of special MCU pins has to be set-up to proper logical values to enter the bootloader. The pins are named BOOT0 and BOOT1 on the STM32 microcontroller. Boot pins can select several modes of bootloader operation:

| BOOT1  | BOOT0  | Boot Mode         | Aliasing                                    |
| ------ | ------ | ----------------- | ------------------------------------------- |
| X      | 0      | Main Flash Memory | Main flash memory is selected as boot space |
| 0      | 1      | System Memory     | System memory is selected as boot space     |
| 1      | 1      | Embedded SRAM     | Embedded SRAM is selected as boot space     |

## Pinout

![image](https://user-images.githubusercontent.com/1549028/213869634-1ede5169-8cdf-4ff9-8a94-26daba5fbd69.png)

## Schematics

![image](https://user-images.githubusercontent.com/1549028/213869613-a7071a58-811e-42a3-b75f-5759ac5d6baa.png)

## Resources

- [Setting-up cross compiler and build tools for STM32](https://freeelectron.ro/arm-cross-compiler-tutorial-stm32/)
- [Bare Metal STM32 Programming – LED Blink](https://freeelectron.ro/bare-metal-stm32-led-blink/)
- [Bare Metal - From zero to blink](https://www.linuxembedded.fr/2021/02/bare-metal-from-zero-to-blink)
- [STM32 toolchain for Windows](https://embeddedgeek.net/posts/STM32-toolchain-for-windows/)
- [STM32 toolchain for Windows - Part 1 (CubeMX, GCC, Make and OpenOCD)](https://youtu.be/PxQw5_7yI8Q)
- [Visual Studio Code for STM32 development and debugging - Part 2](https://youtu.be/xaC5oWwzOt0)
- [STM32 Startup code, Bare metal - Part 3](https://youtu.be/7stymN3eYw0)
- [stm32-for-vscode](https://marketplace.visualstudio.com/items?itemName=bmd.stm32-for-vscode)
- [Program STM32F4 with UART](http://stm32f4-discovery.net/2014/09/program-stm32f4-with-uart/)
- [STM32 LED Blink](https://stm32world.com/wiki/STM32_LED_Blink)
- [Blinking LED with STM32CubeMX and HAL](https://wiki.st.com/stm32mcu/wiki/STM32StepByStep:Step2_Blink_LED)
- [UART and new board introduction](https://wiki.st.com/stm32mcu/wiki/STM32StepByStep:Step3_Introduction_to_the_UART)
- [Using the STM32 UART interface with HAL ](https://visualgdb.com/tutorials/arm/stm32/uart/hal/)
- [st-flash tool](https://github.com/stlink-org/stlink)
