# VSCP Firmware

The VSCP firmware is the software that runs on a VSCP node. It implements the VSCP protocol and provides the functionality needed to make the node work in a VSCP network. The firmware is written in C and is designed to be portable to different microcontroller architectures.

The firmware is divided into two main parts: the core and the application. The core implements the VSCP protocol and provides the basic functionality needed to communicate with other nodes in the network. The application implements the specific functionality of the node, such as reading sensors or controlling actuators.

There are two versions of the VSCP firmware: Level I and Level II. Level I is designed for small microcontrollers with limited resources, while Level II is designed for more capable microcontrollers or small embedded computers.

You adopt the VSCP firmware source by implementing a couple of callbacks. These callbacks are used to interface the firmware with the specific hardware of the node. The callbacks are used to read and write registers, send and receive VSCP events, and perform other hardware-specific tasks.

