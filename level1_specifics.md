# VSCP Level I

VSCP is divided into two levels intended for different types of data transports and hardware. Level I is for smaller hardware devices with limited resources and Level II is for devices with more resources.

## Addressing
VSCP hardware (we call each device a node hereafter) is always identified by a 128-bit [GUID (Globally Unique ID)](http://docs.vscp.org/spec/latest/#/./vscp_globally_unique_identifiers). As it's name says this id is world unique identifier. A GUID series can be received by sending a request to (guid-request@vscp.org) or they can be constructed from other unique address ranges such as the Ethernet MAC address, an IP-address or some other readably aailable sources.

A Level II device usually use the full 16-byte GUID of the node to identify itself when it send events (messages are called events in VSCP). For a small system this would be a big waste of both bandwidth and memory resources so they instead use nicknames. Nicknames can be 8-bit, 16-bit or even 32-bit id''s that are used as a one to one id for the nodes GUID. For CAN4VSCP nodes for example this id is an 8-bit id.


Most events in VSCP is not addressed but have an origin.

## Init. button.
A Level I node should have an init. button that when hold for a few seconds init. the device. Normally this means that a nickname discovery is performed, meaning the nod eis getting a nickname to use instead of its full GUID. 

The same button can be used for a factory default setting operation if hold for a longer time.

## Status LED
A level I device should have a LED which is blinking when the node is initializing, doing nickname discover and which lights steady when the node is in it's active state.

## Registers
VSCP requires some registers to be presented in each node. This is registers with special meaing such as the GUID, discussed above, the MDF (Module Description File) url, alarmflags and quite a few others. You can read more about VSCP regsisters in the spceification [here](http://docs.vscp.org/spec/latest/#/./vscp_register_abstraction_model).

Both LevelI and Level II nodes have registers. The difference is that Level I nodes can address pages with 128 registers while a Level II node can address pages with 4294967168 8-bit registers. Both have the standard registers at the 128 byte at the top of the register space.

As a hardrware/firmware developer registers are important. At higher levels, typically user space, the content is presented as abstract types such as stings, floats and ints. But they all start as items stored in register positions for the hardware/firmware developer.

Registers are often refered to as the VSCP register abstraction model as it lay out configuration data and read out data in an 8-bit regsister address space. On many nodes this is just how the data is presented. The internal definition can be something different all together.

## Decision Matrix
The decision matrix for a Level I node is smaller in width than it is for a Level II node. You can read all about it [here](http://docs.vscp.org/spec/latest/#/./vscp_decision_matrix).

The decision matrix is a standrad way to make a node user "programmable" in terms functionality. Typicall with

* If that event is received fo this.
* If that hapens do something.

Some real life samples can be if

* If a [CLASS1.CONTROL, Type=26,Sync](http://docs.vscp.org/spec/latest/#/./class1.control?id=type26-0x1a-sync) event is received send measurement data for sensor 1, 6 and 9.
* Set outputs high of a digital module when a specific event is received.

Some samples on a Level I real world node **Beijing** is [here](https://www.grodansparadis.com/beijing/manual/doku.php?id=decisionmatrix).


## Decide which Level to build for
If you have the resources to handle [Level II events](http://docs.vscp.org/spec/latest/#/./level_ii_events) you should definitly build a Level II node instead of a Level I node. 

If you don't have the resources to handel Level II events in your hardware go for Level I. Let a VSCP daemon or gateway do the translation to full VSCP level II events for you. 

## Firmware

Firmware for Level I nodes is available in the [vscp-firmware repository](https://github.com/grodansparadis/vscp-firmware). **vscp_firmware.h** and **vscp_firmware.c** in the top common folder is the file you are looking for.Read the rest of this sections on how to implement this code into your hardware device.

An alternative is to use vscp-firmware-level2.h/vscp-firmware-level2.c which can be used to build both level I and level II firmware.

As an alternative to the code described here you can use the [vscp-framework](https://github.com/BlueAndi/vscp-framework) to implement a Level I device. Also [vscp-arduino](https://github.com/BlueAndi/vscp-arduino) can be useful.


[filename](./bottom_copyright.md ':include')