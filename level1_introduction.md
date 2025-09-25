# Introduction

Level I of the VSCP standard is made to be used in small and simple devices. A typical device is based on an 8-bit microcontroller with limited memory and processing power. The protocol is designed to be simple to implement and to use as little resources as possible. If you have hardware with more resources - like a 32-bit microcontroller - you can of course use level I but you can also consider using level II of the VSCP standard. See [here](./level2_introduction.md) for more information about level II. Level I is a subset of level II so if you implement level II you also implement level I.



To get started, first of all you need the firmware code. You find it [here on github](https://github.com/grodansparadis/vscp-firmware) together with a lot of samples. The common folder holds the firmware code. On set of files for each level (1/2) of the VSCP protocol (files for level II can actually be used to build lebvel I firmware to). Samples are organized in there respective architecture with a common folder at each level that contains common code for that level. Also search for "vscp-demo" [here](https://github.com/grodansparadis?tab=repositories) for working samples on different platforms.

Two projects of [Grodans Paradis AB](https://grodansparadis.com) is maintained as reference projects that is always will be open and kept up to date with the standard. Both are CAN oriented and written for the Microchip PIC processor but are general in there implementation so they can be used as starting points also for wireless or other implementations of a VSCP node.

## License

The firmware files are release under the [MIT license](https://opensource.org/licenses/MIT) so you are free to use them both in free and commercial projects without restrictions.

## Reference code

The first reference node is [Paris](https://github.com/grodansparadis/can4vscp-paris) which is a relay control node. 

The second reference node is [Kelvin NTC10K](https://github.com/grodansparadis/can4vscp-kelvin-ntc10k) which is a temperature sensor node.

You find other code for AVR/ARM etc in the [firmware folder](https://github.com/grodansparadis/vscp-firmware). Some old and some new.

## The code

The common VSCP node firmware code is [vscp-firmware.c](https://github.com/grodansparadis/vscp-firmware/blob/master/common/vscp-firmware.c) and [vscp-firmware.h](https://github.com/grodansparadis/vscp-firmware/blob/master/common/vscp-firmware.h). Together they implement the full functionality of a Level I VSCP node. 

If you look in vscp-firmware.c  which include files that are needed 

```c
#include `<string.h>`

#include `<stdlib.h>`
#include "vscp-class.h"

#include "vscp-type.h"
#include "vscp-firmware.h"

```

you see the references to [vscp-class.h](https://github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp-class.h) and [vscp-type.h](https://github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp-type.h). Both of these files come from the [VSCP main project](https://github.com/grodansparadis/vscp_software) but they are duplicated in the vscp-firmware/common folder. 

So before you start your project


*  git clone https://github.com/grodansparadis/vscp-firmware
*  Include vscp-firmware.c and vscp-firmware.h to your own project.
*  Add vscp-firmware/common to your include path.


[filename](./bottom_copyright.md ':include')

