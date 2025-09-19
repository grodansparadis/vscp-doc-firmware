# VSCP Levels

The VSCP protocol is divided into two levels: Level I and Level II. The main difference between the two levels is the size of the data part of VSCP events which is 0-8 bytes for level I and 0-512 bytes for level II.

Level I is designed for small microcontrollers with limited resources, while Level II is designed for more capable microcontrollers or small embedded computers.

You can choose which level to use based on the capabilities of your device and the requirements of your application. If your device has limited resources and you only need to send small amounts of data, Level I may be a good choice. If your device has more resources and you need to send larger amounts of data, Level II may be a better choice. Level II include all functionality in Level I so if you can handle level II events, it is a good choice.

