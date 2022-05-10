# Adafruit_ILI9486_ESP32

Transform the 3.5" TFT display in **parallel** to **SPI** with the 74HC595 for use with the ESP32.

We found Reducing the number of display pins or other devices that use a lot of wires and use parallel connection Often choose to convert it to a serial connection instead. will reduce the number of calls but may be traded at a lower speed Converting to a serial connection usually uses two bus types: SPI and I2C.

In the past, some TFT LCD monitors and some manufacturers already have an SPI connection. Makes it possible to connect via SPI, but for this article, we have chosen a monitor that does not have SPI built-in, causing the need to build a conversion circuit by yourself.

# Different pins of the TFT LCD screen

TFT LCD screen can be divided into 2 different groups of pins as follows

> # DATA group pin
It is a pin preceded by D.. It is used to transmit and receive data in parallel. The number of pins in this group is usually 8 pins or 16 pins, but in color data transmission, it is transmitted at 16 bits as well, so if there are 8 pins in this group, color data transmission must be sent 2 rounds of 8 bits each to complete. 16 bits, but if there are 16 pins, it will send 16 bits of color at once. The number of pins in this group is directly related to the speed of the screen in the case of parallel connections. Because between two 8-bit transmissions and 16-bit transmissions at one time, a single 16-bit transmission is already faster. but if it is a serial connection No matter how many legs you have, it has little effect on speed. Because whether it is 8-pin or 16-pin, it must be sent at 16 bits anyway (because the color has to be sent 16 bits).

> # Control group pin
This group of legs has a total of 5 legs, consisting of different legs that have the following functions:

* RD - Used to control read / write data to the screen. By finding this pin has a logic of 1, it uses the write mode, but if it is 0, it uses the mode to read data from the screen.
* WR - is comparable to the EN pin on the Character LCD. It controls the display to receive data. or send out information Usually there is a logic of 1, but when there is a logic of 0 -> 1, the monitor immediately receives input/output.
* RS - Used to tell that the data that is being sent to the screen is a command or data. If it's logic 0 it's an instruction and if it's logic 1 it's data.
* CS - Used to order the monitor to be ready for data input/output, normally when sending/receiving data will be set to logic 0 and will be set to 1. (but the screen still shows the same Just while being Logic 1, it can't send/receive any data.)
* RST - Used to reset the screen.
This group of legs can be reduced up to 2 legs:

* RD continues to Logic 1 to keep the screen in the input state only.
* WR is connected with SCK pin when converting to SPI because of the display driver chip. The 74HC595 is more complex than the 74HC595, making the driver chip response slower. As a result, the 74HC595 has been updated. The screen receives the input data after the 74HC59 completes the data update.

# Change all DATA pins to SPI with 74HC595
The 74HC595 IC is a Shift Register IC already sold by the shop. With a DIP-16 body, it is easy to test on a protoboard. or assembled on a multipurpose printing plate It also doesn't require any other peripherals, making it easy to use. And it's not very difficult.

Circuit Connection

![](https://cz.lnwfile.com/_/cz/_raw/5c/66/or.png)
In the circuit, there will be a pin that is not specified, which is Pin 16 of the 74HC595. It is a VCC. Combine it with the 5V port of the screen and supply 5V voltage to it. The 8th pin is GND to be connected to the GND of the ESP32.

# Connecting to ESP32
| Screen set + 74HC595 | ESP32  |
|--|--|
| RST | GPIO27 |
| RS|  GPIO26 |
|CS|GPIO25|
| SCK| HSPI_GPIO14 |
| MOSI | HSPI_GPIO13 |
|SS/CS|HSPI_GPIO15|


# Based on 

Adafruit_ILI9486_STM32 lib: (https://github.com/stevstrong/Adafruit_ILI9486_STM32)

modified to support ESP32 by IOXhop (www.ioxhop.com)

Transform the 3.5" TFT display in parallel to SPI with the 74HC595 for use with the ESP32 : (https://www.artronshop.co.th/article/74

Note: 74HC595 use 5V for VCC
