# Introduction

First of all you need the firmware code. You find it [here on github](https://github.com/grodansparadis/vscp_firmware) together with a lot of samples. The common folder holds the firmware code. On set of files for each level (1/2) of the VSCP protocol (files for level II can actually be used to build lebvel I firmware to). Samples are organized in there respective architecture with a common folder at each level that contains common code for that level. Also search for "vscp-demo" [here](https://github.com/grodansparadis?tab=repositories) for working samples on different platforms.

Two projects of [Grodans Paradis AB](https://grodansparadis.com) is maintained as reference projects that is always will be open and kept up to date with the standard. Both are CAN oriented and written for the Microchip PIC processor but are general in there implementation so they can be used as starting points also for wireless or other implementations of a VSCP node.

## License

The firmware files are release under the [MIT license](https://opensource.org/licenses/MIT) so you are free to use them both in free and commercial projects without restrictions.

## Reference code

The first reference node is [https://grodansparadis.com/paris/paris.htmlParis](https://grodansparadis.com/paris/paris.htmlParis) which is a relay control node. Follow [the link](https://grodansparadis.com/paris/paris.html) to get information about it. The code for the node is [here at Github](https://github.com/grodansparadis/can4vscp_paris).

The second reference node is [https://www.grodansparadis.com/kelvinntc10k/kelvin_ntc10ka.htmlKelvin NTC](https://www.grodansparadis.com/kelvinntc10k/kelvin_ntc10ka.htmlKelvin NTC) which is a temperature sensor node. Follow [the link](https://www.grodansparadis.com/kelvinntc10k/kelvin_ntc10ka.html) to get information about it. The code for the node is [here at Github](https://github.com/grodansparadis/can4vscp_kelvin_ntc10k).

You find other code for AVR/ARM etc in the firmware folder. Some old and some new.

## The code

The common VSCP node firmware code is [vscp_firmware.c](https://github.com/grodansparadis/vscp_firmware/blob/master/common/vscp_firmware.c) and [vscp_firmware.h](https://github.com/grodansparadis/vscp_firmware/blob/master/common/vscp_firmware.h). Together they implement the full functionality of a Level I VSCP node. 

If you look in vscp_firmware.c  which include files that are needed 

```c
#include `<string.h>`

#include `<stdlib.h>`
#include "vscp_class.h"

#include "vscp_type.h"
#include "vscp_firmware.h"

```

you see the references to [vscp_class.h](https://github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp_class.h) and [vscp_type.h](https://github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp_type.h). Both of these files come from the [VSCP main project](https://github.com/grodansparadis/vscp_software) but they are duplicated in the vscp_firmware/common folder. 

So before you start your project


*  git clone https://github.com/grodansparadis/vscp_firmware
*  Include vscp_firmware.c and vscp_firmware.h to your own project.
*  Add vscp_firmware/common to your include path.


[filename](./bottom_copyright.md ':include')

