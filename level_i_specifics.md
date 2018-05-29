#VSCP Level I

VSCP is divided into two levels intended for different types of data transports and hardware. Level I is for smaller hardware devices with limited resources and Level II is for devices with more resources.

## Addressing
VSCP hardware (we call each device a node hereafter) is always identified by a 128-bit [GUID (Globally Unique ID)](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_globally_unique_identifiers.html). As it's name says this id is world unique identifier. A GUID series can be received by sending a request to (guid-request@vscp.org) or they can be constructed from other unique address ranges such as the Ethernet MAC address, an IP-address or some other readably aailable sources.

A Level II device usually use the full 16-byte GUID of the node to identify itself when it send events (messages are called events in VSCP). For a small system this would be a big waste of both bandwidth and memory resources so they instead use nicknames. Nicknames can be 8-bit, 16-bit or even 32-bit id''s that are used as a one to one id for the nodes GUID. For CAN4VSCP nodes for example this id is an 8-bit id.


Most events in VSCP is not addressed but have an origin.

## Registers
VSCP requires some registers to be presented in each node. This is registers with special meaing such as the GUID, discussed above, the MDF (Module Description File) url, alarmflags and quite a few others. You can read more about VSCP regsisters in the spceification [here](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_register_abstraction_model.html).

Both LevelI and Level II nodes have registers. The difference is that Level I nodes can address pages with 128 registers while a Level II node can address pages with 4294967168 8-bit registers. Both have the standard registers at the 128 byte at the top of the register space.

As a hardrware/firmware developer registers are important. At higher levels, typically user space, the content is presented as abstract types such as stings, floats and ints. But they all start as items stored in register positions for the hardware/firmware developer.

Registers are often refered to as the VSCP register abstraction model as it lay out configuration data and read out data in an 8-bit regsister address space. On many nodes this is just how the data is presented. The internal definition can be something different all together.

## Decision Matrix
The decision matrix for a Level I node is smaller in width than it is for a Level II node. You can read all about it [here](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_decision_matrix.html).

The decision matrix is a standrad way to make a node user "programmable" in terms functionality. Typicall with

* If that event is received fo this.
* If that hapens do something.

Some real life samples can be if

* If a [CLASS1.CONTROL, Type=26,Sync](https://grodansparadis.gitbooks.io/the-vscp-specification/content/class1.control.html#type--26-0x1a-sync) event is received send measurement data for sensor 1, 6 and 9.
* Set outputs high of a digital module when a specific event is received.

Some samples on a Level I real world node **Beijing** is [here](http://www.grodansparadis.com/beijing/manual/doku.php?id=decisionmatrix).


## Decide which Level to build for
If you have the resources to handle [Level II events](https://grodansparadis.gitbooks.io/the-vscp-specification/content/level_ii_events.html) you should definitly build a Level II node instead of a Level I node. 

If you don't have the resources to handel Level II events in your hardware go for Level I. Let a VSCP daemon or gateway do the translation to full VSCP level II events for you. 


{% include "./bottom_copyright.md" %}