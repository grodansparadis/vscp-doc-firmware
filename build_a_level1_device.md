# Building a Level I Device

To build a VSCP Level I device you typically need a small microcontroller with at least 8 kB of flash memory and 512 bytes of RAM. If the device has a CAN interface it is a possible way to connect the device to the rest of the world. But the VSCP protocol is independent of the physical layer so you can use any other physical layer if you want.

A good choice of microcontroller is a Microchip PIC18F series or a Microchip PIC24F series. Both these families have a CAN interface and are easy to program using free tools from Microchip.

You can also use an 8-bit AVR microcontroller from Microchip (formerly Atmel) or a 32-bit ARM Cortex-M microcontroller. There are many to choose from so pick one that you like and that has the features you need.

To get started you can download the VSCP Level I reference implementation from [github](https://github.com/grodansparadis/vscp-firmware). This code is written for a PIC18F series microcontroller but can be ported to other microcontrollers with some effort.  
The code is written in C and is easy to understand. It is also well documented so you can learn a lot by reading the code. The code is released under the MIT license so you can use it in your own projects without any restrictions.

[filename](./bottom_copyright.md ':include')