# Introduction Level II

The VSCP Level II is made to be used in more capable devices then what level I is designed for. If a level I device typically is an 8-bit microcontroller with limited memory and processing power, a level II device is typically a 32-bit microcontroller or a small embedded computer with more memory and processing power. 

The main difference between the two levels is the size of the data part of VSCP events which is 0-8 bytes for level I and 0-512 bytes for level II.

Actually most systems today are able to handle level II events so if you are in doubt, use level II.

To implement level II support in your device, you can start by looking at the firmware code [here on github](https://github.com/grodansparadis/vscp-firmware). 






[filename](./bottom_copyright.md ':include')