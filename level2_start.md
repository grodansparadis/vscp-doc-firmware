# VSCP Level II Firmware

This part describes how to build a Level II hardware device.

The VSCP firmware is code that you find in hardware devices that control things. It is built to be able to fit in the smallest possible device and it is free from any assumptions on which transport mechanism that is used. This is the same for both Level I and Level II devices.

It is not a big deal to make a node that follow the the VSCP standard. You should however be aware of what [registers](http://docs.vscp.org/spec/latest/#/./vscp_register_abstraction_model) are, what [events](http://docs.vscp.org/spec/latest/#/./level_i_events) are, what a [MDF](http://docs.vscp.org/spec/latest/#/./vscp_module_description_file) is, what a [decision matrix (DM)](http://docs.vscp.org/spec/latest/#/./vscp_decision_matrix) is and what a [GUID](http://docs.vscp.org/spec/latest/#/./vscp_globally_unique_identifiers) is before you start. You find all that information in the [VSCP Specification](http://docs.vscp.org/spec/latest).

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid-request@vscp.org](mailto:guid-request@vscp.org) providing your contact information and ask for a series. You get a series free of charge.

##  Before you go

You have to chose what type of [transport layer/low level protocol](http://docs.vscp.org/spec/latest/#/./physical_level_lower_level_protocols) you want to implement in your node. Basically there is currently two types. Packet based or command based. You can also skip implementation of a client interface all together and instead actively connect with your node itself to a VSCP daemon/server instead of waiting for connection. The later with the drawback that it depends on a central server.

Normally you should implement all the required parts of VSCP, but if you are on very constraint hardware and still want Level II you also have the option to implement a [dumb node](http://docs.vscp.org/spec/latest/#/./vscp_dumb). A dumb node only has to implement an event sender and send a node heartbeat.

## Packet based nodes (Ethernet/UDP/Multicast)

This is a model that works much the same as for Level I firmware. The firmware received VSCP events and send VSCP events. All nodes should implement the heartbeat and capabilities event. Described later.

## Web connection oriented nodes (REST/websockets)
Both requires a central server. Described later.

## VCSP Link interface based nodes (TCP/IP)

The [VSCP Link interface](https://grodansparadis.github.io/vscp-doc-spec/#/./vscp_tcpiplink) is a telnet interface. It should always handles the following commands

 | Command | Description | 
 | ------- | ----------- | 
 | **NOOP**           | No operation. The response is always "+OK"               | 
 | **QUIT**           | Close the connection                                     | 
 | **USER**           | Username credentials used to login to the interface.     | 
 | **PASS**           | Password credentials used to login to the interface.     | 
 | **CHALLENGE**      | Used to get a session unique id. Part of log in process. | 
 | **SEND**           | Send event to node.                                      | 
 | **RETR**           | Get event from node.                                     | 
 | **RCVLOOP**        | Enter receive loop.                                      | 
 | **QUITLOOP**       | Terminates/end receive loop.                             | 
 | **CDTA/CHKDATA**   | Check if there is events waiting to be received.         | 
 | **CLRA/CLRALL**    | Clear event input queue.                                 | 
 | **STAT**           | Get statistical information from node.                   | 
 | **INFO**           | Get status information from node.                        | 
 | **CHID/GETCHID**   | Get channel id from node.                                | 
 | **SGID/SETGUID**   | Set GUID for node.                                       | 
 | **GGID/GETGID**    | Get GUID for node.                                       | 
 | **VERS/VERSION**   | Get version for node firmware.                           | 
 | **SFLT/SETFILTER** | Set event filter on interface.                           | 
 | **SMSK/SETMASK**   | Set event mask on interface.                             | 
 | **WCYD / WHATCANYOUDO** | Ask for capabilities of device.                     |
 | **INTERFACE** | Handle interfaces of device.                     |

## Your own transport. 

Yes of course you can create your own frame format or transport mechanism as to your needs.Just make sure that you understand the VSCP event format.

A full level II event is defines as:

```c
  uint16_t crc; /* crc checksum (calculated from here to end) */
                /* Used for UDP/Ethernet etc */

  uint32_t obid; /* Used by driver for channel info etc. */

  /* Time block - Always UTC time. I all zero set current time on receiving end */
  uint16_t year;
  uint8_t month;  /* 1-12 */
  uint8_t day;    /* 1-31 */
  uint8_t hour;   /* 0-23 */
  uint8_t minute; /* 0-59 */
  uint8_t second; /* 0-59 */

  uint32_t timestamp; /* Relative time stamp for package in microseconds */
                      /* ~71 minutes before roll over */
                      /* If all zero set relative time on receiving end */

  /* ----- CRC should be calculated from here to end + data block ----  */

  /*
      Bit 15 - This is a dumb node. No MDF, register, nothing.
      Bit 14 - GUID type
      Bit 13 - GUID type
      Bit 12 - GUID type (GUID is IP v.6 address.)
      Bit 8-11 = Reserved
      Bit 765 =  priority, Priority 0-7 where 0 is highest.
      Bit 4 = hard coded, true for a hard coded device.
      Bit 3 = Don't calculate CRC, false for CRC usage.
            Just checked when CRC is used.
            If set the CRC should be set to 0xAA55 for
            the event to be accepted without a CRC check.
      Bit 2 = Rolling index.
      Bit 1 = Rolling index.
      Bit 0 = Rolling index.
  */
  uint16_t head;

  uint16_t vscp_class; /* VSCP class */
  uint16_t vscp_type;  /* VSCP type */
  uint8_t GUID[16];    /* Node globally unique id MSB(0) -> LSB(15) */
  uint16_t sizeData;   /* Number of valid data bytes */

  uint8_t *pdata; /* Pointer to data. Max 512 bytes */
```

A lot of information that should be transferred. The good news is that a lot of the fields can be set to zero and the receiving end will set them to a valid value. The fields that can be set to zero are:

- Time block (if all zero set to current time on receiving end)
- Timestamp (if all zero set to current time on receiving end)
- obid if not used.
- crc if not used.

But transferring a lot of zeroes does not help on bandwidth. But if you set up your own transport you can of course skip all fields that are not used. Just make sure that the receiving end understands what you are doing.

Generally you can skip sending information that the receiving end already knows.

  * If you know who you are talking to, then GUID does not matter at all. You can set it on the receiving end.
  * If you know how your measurements are encoded, then there is no reason to send information about the encoding.
  * If you know which device a measurement has that is being sent, then there is no reason to send information about it.
  * If the time for your data is not critical, then no timestamp is needed. Set it on the receiving end.
  * If date and time are not critical, then they are not needed, set them on the receiving end.

### Example 1 – Two devices exchange information

**Device A** is a sensor that measures temperature and humidity.
**Device B** is a unit that sends this information out to other VSCP devices.

We know which device sends the measurement values. The GUID is known.
Measurements are not time-critical. A timestamp is not needed; both can be set on the receiver side.We know the units for the measurements: degrees Celsius and percent for the hygrometer. Therefore, this information does not need to be sent across the line, etc.

So now we can send a measurement value as a frame containing

"19.2,47"

which represents 19.2 degrees Celsius and 47% humidity. But we could just as well send the same information encoded in binary, or in some other fun way. As long as we agree on the format on the sending side and the receiving side.

On the receiving side, the timestamp, date and time, GUID, and values are set and encoded in one of the ways that VSCP supports. All this resulting in a standard VSCP event that the rest of the system understands.

If there on the other hand are three sensors transmitting this information, the sensor index can be coded as A/B/C, and you send

"A:19.2,47"

where ‘A’ indicates that the measurement values come from sensor ‘A’.

This can, of course, be made as complex as you like. At some level of complexity, it eventually becomes easier to just use all the fields of VSCP.

[JSON](https://grodansparadis.github.io/vscp-doc-spec/#/./vscp_level_ii_specifics?id=json-representation) and [XML](https://grodansparadis.github.io/vscp-doc-spec/#/./vscp_level_ii_specifics?id=xml-representation) are also options for encoding the information. But remember that these formats are more verbose and require more bandwidth. The same thing apply here however that fields that are known on the receiving end do not need to be sent.

## Define registers
Define all registers that your node should have. Your device must define the standard registers defined in the [VSCP specification](http://docs.vscp.org/spec/latest/#/./vscp_register_abstraction_model) and you can define your own. 

Also for a level II node all registers is 8-bit wide. But the register space is larger. There is a 32-bit address for registers. 0xff000000 - 0xffffffff is reserved for standard VSCP registers. You can use 0x00000000 - 0xfeffffff for your own registers.


## Define remote variables
Define all remote variables that your node should have. You do not need to define any remote variables if you don't want but they simplify handling of higher level data types.

## Define the events your device will send/receive.
Most nodes send/receive some events. Identify them. This is not the protocol events used by the VSCP firmware itself. This is the events that your node will send/receive to/from other nodes.

## Create your MDF file
Create an MDF file that describes your node. You can use the MDF editor of the [VSCP Works](https://grodansparadis.github.io/vscp-works-qt/#/) application to create your MDF file. The MDF file should describe all registers and remote variables that your node has. It should also describe what events that your node can send and receive. You can choose to crete it in XML or JSON format. The file should eventually be placed on an open web server so that it can be fetched by other applications.

##  Callbacks from VSCP firmware to your application

The VSCP firmware is made to be as generic as possible. It does not know what your application is doing. Therefore, there are some callbacks that you can implement in your application code that the VSCP firmware will call when something happens.You have to implement these callbacks in your application code. If you do not implement them, the firmware will not compile.

All needed callbacks are described here: [VSCP level II callbacks](./level2_callbacks.md 'Level II callbacks from VSCP firmware to your application')

Callbacks send and receive events. Read/write registers. Handle the decision matrix etc.

## Crete your application code
Create your application code. This is the code that implements the functionality of your node. It uses the callbacks to interact with the VSCP firmware.


[filename](./bottom_copyright.md ':include')







