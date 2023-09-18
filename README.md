# Getting Started with STM32 Programming

The BluePill STM32F103C8 board is used for this project:

- **Step 1:** Configure using STM32CubeMX and create a Makefile project
- **Step 2:** Write a simple LED blinky program on GPIO PC13
- **Step 3:** Print "Hello World!" on UART1

![image](https://github.com/m3y54m/start-stm32-bluepill/assets/1549028/1b6a010c-b83e-449e-8bc2-77c649784068)


## Uploading

The best way to upload the sketch is to use ST-Link or J-Link debuggers. But due to the additional hardware costs you may want to use a cheaper alternative method.

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


## Notes

- Adafruit SSD1306 LCD driver library did not work with the BluePill. I used [U8glib](https://github.com/olikraus/u8g2/) instead.

## Resources

- [STM32 toolchain for Windows](https://embeddedgeek.net/youtube/stm32-toolchain-for-windows/)
- [STM32 toolchain for Windows - Part 1 (CubeMX, GCC, Make and OpenOCD)](https://youtu.be/PxQw5_7yI8Q)
- [Visual Studio Code for STM32 development and debugging - Part 2](https://youtu.be/xaC5oWwzOt0)
- [STM32 Startup code, Bare metal - Part 3](https://youtu.be/7stymN3eYw0)
- [stm32-for-vscode](https://marketplace.visualstudio.com/items?itemName=bmd.stm32-for-vscode)
- [Program STM32F4 with UART](http://stm32f4-discovery.net/2014/09/program-stm32f4-with-uart/)
- [STM32 LED Blink](https://stm32world.com/wiki/STM32_LED_Blink)
- [Blinking LED with STM32CubeMX and HAL](https://wiki.st.com/stm32mcu/wiki/STM32StepByStep:Step2_Blink_LED)
- [UART and new board introduction](https://wiki.st.com/stm32mcu/wiki/STM32StepByStep:Step3_Introduction_to_the_UART)
- [Using the STM32 UART interface with HAL ](https://visualgdb.com/tutorials/arm/stm32/uart/hal/)
