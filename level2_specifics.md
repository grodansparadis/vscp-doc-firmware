
# VSCP Level II Firmware

This part desctibes how to build a Level II hardware device.

The VSCP firmware is code that you find in nodes. It is built to be able to fit in the smallest possible device and it is free of assumptions on which transport mechanism that is used. This is the same for both Level I and Level II devices.

It is not a big deal to make a node that follow the the VSCP standard. You should however be aware of what [registers](http://docs.vscp.org/spec/latest/#/./vscp_register_abstraction_model) are, what [events](http://docs.vscp.org/spec/latest/#/./level_i_events) are, what a [MDF](http://docs.vscp.org/spec/latest/#/./vscp_module_description_file) is, what a [decision matrix (DM)](http://docs.vscp.org/spec/latest/#/./vscp_decision_matrix) is and what a [GUID](http://docs.vscp.org/spec/latest/#/./vscp_globally_unique_identifiers) is before you start. You find all that information in the [VSCP Specification](http://docs.vscp.org/spec/latest).

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid-request@vscp.org](mailto:guid-request@vscp.org) providing your contact information and ask for a series. You get a series free of charge.

##  Before you go

You have to chose what type of [transport layer/low level protocol](http://docs.vscp.org/spec/latest/#/./physical_level_lower_level_protocols) you want to implement in your node. Basically there is currently two types. Packet based or command based. You can also skip implemention of a client interface all together and instead actively connect with your node itself to a VSCP daemon/server instead of waiting for connection. The later with the drawback that it depes on a central server.

Normally you should implement all the required parts of VSCP but if you are on very constraint hardware and still want Level II you also have the option to implement a [dumb node](http://docs.vscp.org/spec/latest/#/./vscp_dumb). A dumb node only hasveto implement an event sender and send a node heartbeat.

## Packet based nodes (Ethernet/UDP/Multicast)

This is a model that works much the same as for Level I firmware. The firmware received VSCP events and send VSCP events. All nodes should implement the heartbeat and capabilities event. Described later.

## Web connetion orinted nodes (REST/websockets)
This two requires a central server. Described later.

## VCSP Link interface based nodes (TCP/IP)

The VSCP Link interface is a telnet interface which is a subset of the [VSCP daemon tcp/ip interface](http://docs.vscp.org/vscpd/latest/#/tcp_ip_control_interface). It always handles the following commands

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
 | **DM**             | Decision matrix handling functionality. (optional)       | 
 | **VAR**            | Variable handling functionality. (optional)              | 

### VAR

The variable handling functionality makes it possible to read registers on a device. 

#### Subcommands

 | Subcommand     | Description |          
 | ----------     | -------------          
 | **READ**       | Read a variable.        | 
 | **WRITE**      | Write a variable.       | 
 | **READVALUE**  | Read a variable value.  | 
 | **WRITEVALUE** | Write a variable value. | 

Registers are defined by **page,register**. So 

    VAR READVALUE REG,0,11

will return content of register 11 on page 0 as

    register - value
    +OK

In the same manner you write a register with

    VAR WRITEVALUE REG,0,11,value

where value is the new content that should be written to the register.

Abstractions defined in the MDF can be written and read using there name

    VAR READVALUE version
 
will return 

    version
    +OK

and

    VAR WRITEVALUE version value

will set the version abstraction to *value*.


### DM

Can be used to read and write decision matrix rows.

#### Subcommands


[filename](./bottom_copyright.md ':include')









