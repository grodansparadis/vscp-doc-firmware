# Callbacks

The real functionality of the framework is set by how you handle the callbacks. Here we will go through each of them in turn. You find them all defined in [vscp_firmware.h](https///github.com/grodansparadis/vscp_firmware/blob/master/common/vscp_firmware.h)

Just implement dummys for them to start with if you like.  The one you probably want to implement from start is read/write event and register read/write.
##  getVSCPFrame

`<code=c>`
int8_t getVSCPFrame(uint16_t *pvscpclass,
                      uint8_t *pvscptype,
                      uint8_t *pNodeId,
                      uint8_t *pPriority,
                      uint8_t *pSize,
                      uint8_t *pData);
`</code>`
The VSCP firmware code request a new event with this callback. If one is available return TRUE/none zero and fill in the parameters if not just return FALSE/zero.


*  **pvscpclass** - Pointer to a VSCP class, one of the Level I classes defined in the [VSCP specification](http://www.vscp.org/docs/vscpspec/doku.php) and in the [vscp_class.h](https///github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp_class.h) header file.

*  **pvscptype** - Pointer to a  VSCP type, one of the types defined in the [VSCP specification](http://www.vscp.org/docs/vscpspec/doku.php) and in the [vscp_type.h](https///github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp_type.h) header file.

*  **pNodeId** - Pointer ti a  node id that is the origin for this event.

*  **pPriority** - Pointer to priority for the event 0-7 where 0 is the highest priority.

*  **pSize** - Pointer to data size. For level I events 0-8 bytes.

*  **pData** - Pointer to data array of eight bytes.

In the Paris reference code the implementation of this callback read a CAN message and translate it to a VSCP event. Buffering is done in hardware. If you need bigger buffers for some reason this is the place to implement them preferably having interrupts to fill them.

## sendVSCPFrame===== 

`<code=c>`
int8_t sendVSCPFrame(uint16_t vscpclass,
        uint8_t vscptype,
        uint8_t nodeid,
        uint8_t priority,
        uint8_t size,
        uint8_t *pData);
`</code>`

The VSCP firmware code sends events with this callback. If the event is successfully sent return TRUE/none zero and if not return FALSE/zero.


*  **pvscpclass** - Pointer to a VSCP class, one of the Level I classes defined in the [VSCP specification](http://www.vscp.org/docs/vscpspec/doku.php) and in the [vscp_class.h](https///github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp_class.h) header file.

*  **pvscptype** - Pointer to a  VSCP type, one of the types defined in the [VSCP specification](http://www.vscp.org/docs/vscpspec/doku.php) and in the [vscp_type.h](https///github.com/grodansparadis/vscp_software/blob/master/src/vscp/common/vscp_type.h) header file.

*  **pNodeId** - Pointer ti a  node id that is the origin for this event.

*  **pPriority** - Pointer to priority for the event 0-7 where 0 is the highest priority.

*  **pSize** - Pointer to data size. For level I events 0-8 bytes.

*  **pData** - Pointer to data array of eight bytes.

In the Paris reference code the implementation of this callback send a CAN message after waiting a while for a buffer to be available. If you need bigger buffers this is the place to implement them preferably having interrupts to actually send the event on the bus.

##  vscp_getMajorVersion

`<code=c>`
uint8_t vscp_getMajorVersion(void);
`</code>`

You should return the major version for your firmware code 0-255. This is data that will use as a standard register value for version (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)).

##  vscp_getMinorVersion

`<code=c>`
uint8_t vscp_getMinorVersion(void);
`</code>`

You should return the minor version for your firmware code 0-255. This is data that will use as a standard register value for version (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)).


##  vscp_getSubMinorVersion

`<code=c>`
uint8_t vscp_getSubMinorVersion(void);
`</code>`

You should return the major version for your firmware code 0-255. This is data that will use as a standard register value for version (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)).

##  vscp_getGUID

`<code=c>`
uint8_t vscp_getGUID(uint8_t idx);
`</code>`

The [GUID](http://www.vscp.org/docs/vscpspec/doku.php?id=globally_unique_identifiers) of a node is an important part of the nodes identity. If a nickname is just that, a short name, the GUID is the real identity of the node. **IT MUST BE UNIQUE!!!** 

In this callback you should return one of the sixteen bytes of the GUID for your module. In the Paris reference code this is implemented as

`<code=c>`
uint8_t vscp_getGUID(uint8_t idx)
{
    return readEEPROM( VSCP_EEPROM_REG_GUID + idx );
}
`</code>`

as the GUID is stored in EEPROM. But it could equally well be stored in flash memory. The GUID should never be changed during the lifetime of a module. But the EEPROM can still be a convenient place to initialize it in.

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid@vscp.org](guid@vscp.org) providing your contact information and ask for a series. You get a series free of charge. 

##  vscp_setGUID

`<code=c>`
void vscp_setGUID(uint8_t idx, uint8_t data);
`</code>`

The [GUID](http://www.vscp.org/docs/vscpspec/doku.php?id=globally_unique_identifiers) of a node is an important part of the nodes identity. If a nickname is just that, a short name, the GUID is the real identity of the node. **IT MUST BE UNIQUE!!!** 

This callback is only needed if **ENABLE_WRITE_2PROTECTED_LOCATIONS** is defined. If defined this callback is required and it will write the GUID bytes to there respective positions.

The Paris reference module code implement it as

`<code=c>`
#ifdef ENABLE_WRITE_2PROTECTED_LOCATIONS

void vscp_setGUID(uint8_t idx, uint8_t data ) {
    if ( idx>15 ) return;
    writeEEPROM(VSCP_EEPROM_REG_GUID + idx, data);
}
#endif

`</code>`

as the GUID is stored in EEPROM. But it could equally well be stored in flash memory. The GUID should never be changed during the lifetime of a module. But the EEPROM can still be a convenient place to initialize it in.

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid@vscp.org](guid@vscp.org) providing your contact information and ask for a series. You get a series free of charge. 

##  vscp_getUserID

`<code=c>`
uint8_t vscp_getUserID(uint8_t idx);
`</code>`

The user id is a four byte user changeable id that is part of the register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)). With this call the framework reads the bytes.

##  vscp_setUserID

`<code=c>`
void vscp_setUserID(uint8_t idx, uint8_t data);
`</code>`

The user id is a four byte user changeable id that is part of the register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)). With this call the framework write the bytes.

##  vscp_getManufacturerId

`<code=c>`
uint8_t vscp_getManufacturerId(uint8_t idx);
`</code>`

The manufacturer id is a eight byte non user changeable id that is part of the register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)). With this call the framework reads the bytes.

##  vscp_setManufacturerId

`<code=c>`
void vscp_setManufacturerId(uint8_t idx, uint8_t data);
`</code>`

This callback is only needed if **ENABLE_WRITE_2PROTECTED_LOCATIONS** is defined.

The manufacturer id is a eight byte non user changeable id that is part of the register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)). 

With this call the framework write the bytes but only if defined as writable by the define above.

##  vscp_getBootLoaderAlgorithm

`<code=c>`
uint8_t vscp_getBootLoaderAlgorithm(void);
`</code>`

With this callback you should return the bootloader algorithm your module use. You can find the defined bootloader algorithms [here](http://www.vscp.org/docs/vscpspec/doku.php?id=vscp_boot_loader_algorithm). This ís a value that is part of the standard register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)).

##  vscp_getBufferSize

`<code=c>`
uint8_t vscp_getBufferSize(void);
`</code>`

With this callback you should return the buffer size your node uses. This is almost always eight bytes. This is a value that is part of the standard register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)). 

##  getRegisterPagesUsed

`<code=c>`
uint8_t vscp_getRegisterPagesUsed(void);
`</code>`

With this callback you should return the number of pages your node uses. This is a value that is part of the standard register space (see [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model)). 

The callback is deprecated so you can return anything you like. Set this information in the MDF instead.

##  vscp_getMDF_URL

`<code=c>`
uint8_t vscp_getMDF_URL(uint8_t idx);
`</code>`

The [MDF (Module Description File)](http://www.vscp.org/docs/vscpspec/doku.php?id=module_description_file) is probably the most important content of [standard register space](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model#level_i_-_register_abstraction_model). This 32 bit URL points to a XML file that holds all information about your node.

With this callback the bytes (0-31) of the MDF is read. 

You can see [here](https///github.com/grodansparadis/can4vscp_kelvin_ntc10k/tree/master/mdf) and [here](https///github.com/grodansparadis/can4vscp_paris/tree/master/mdf) how a real world MDF looks like.

Typically it is defined in the code as this

`<code=c>`
// The device URL (max 32 characters including null termination)
const uint8_t vscp_deviceURL[] = "www.eurosource.se/paris_010.xml";
`</code>`

##  vscp_readNicknamePermanent

`<code=c>`
uint8_t vscp_readNicknamePermanent(void);
`</code>`

This callback request the nickname of the node.  It should be in persistent storage such as an EEPROM.

##  vscp_writeNicknamePermanent

`<code=c>`
void vscp_writeNicknamePermanent(uint8_t nickname);
`</code>`

This callback writes the nickname of the node to persistent storage. Yes it should be in persistent storage such as an EEPROM.

##  vscp_init_pstorage

`<code=c>`
void vscp_init_pstorage(void);
`</code>`

Initialize persistent storage. This is typically EEPROM or flash that emulates EEPROM. The persistent storage should get content after this operation as it had when it was started the first time.




##  vscp_getControlByte

`<code=c>`
uint8_t vscp_getControlByte(void);
`</code>`

Read the control byte from persistent storage. Yes it should be in persistent storage such as an EEPROM.

The control byte is a code that tells if the node has been initialized or not.

##  vscp_setControlByte

`<code=c>`
void vscp_setControlByte(uint8_t ctrl);
`</code>`

Writes the control byte to persistent storage. Yes it should be in persistent storage such as an EEPROM.

The control byte is a code that tells if the node has been initialized or not.

##  vscp_getPageSelect

`<code=c>`
uint8_t vscp_getPageSelect(uint8_t idx);
`</code>`

Get content of the page select register. idx=0 for MSB of page and idx=1 for LSB of page together forming a 16-byte page value.

The register space is divided in 128 byte pages 0-65525 where the page actually visible is set in this register. Each page is addressed as 0-127 and the standard register space is always available in registers 128-255.

##  vscp_setPageSelect

`<code=c>`
void vscp_setPageSelect(uint8_t idx, uint8_t data);
`</code>`

Set the content of the page select register. idx=0 for MSB of page and idx=1 for LSB of page together forming a 16-byte page value.

The register space is divided in 128 byte pages 0-65525 where the page actually visible is set in this register. Each page is addressed as 0-127 and the standard register space is always available in registers 128-255.

##  vscp_readAppReg

`<code=c>`
uint8_t vscp_readAppReg(uint8_t reg);
`</code>`

This callback request an application register. What you do here is entirely up to you and your application. If your application use more than one page you should check the value of **vscp_page_select** before returning a value. 

If a register that is not defined by your node is requested increase **vscp_errorcnt** and return garbage or zero.

Registers are defined in the [MDF](http://www.vscp.org/docs/vscpspec/doku.php?id=module_description_file) of your node.

##  vscp_writeAppReg

`<code=c>`
uint8_t vscp_writeAppReg(uint8_t reg, uint8_t value);
`</code>`

This callback writes a value to an application register. What you do here is entirely up to you and your application. If your application use more than one page you should check the value of **vscp_page_select** before writing a value. 

If a register that is not defined by your node is requested increase **vscp_errorcnt** and return garbage or the complement of the value that is supposed to be written.

Registers are defined in the [MDF](http://www.vscp.org/docs/vscpspec/doku.php?id=module_description_file) of your node.

Note that the written value should be returned. So if you write a value to EEPROM or something read it also after the write.

##  vscp_getMatrixInfo

`<code=c>`
void vscp_getMatrixInfo(char *pData);
`</code>`

If your node have a decision matrix implemented return a structure consisting of eight bytes with the following content


*  **Byte 0** - Number of DM rows. 0 if none.

*  **Byte 1** - offset in register space.

*  **Byte 2** - start page MSB

*  **Byte 3** - start page LSB

*  **Byte 4** - End page MSB

*  **Byte 5** - End page LSB

*  **Byte 6** - Level II size of DM row (Just for Level II nodes).

##  vscp_getEmbeddedMdfInfo

`<code=c>`
void vscp_getEmbeddedMdfInfo(void);
`</code>`

Get embedded MDF info. If an embedded mdf is available in the device this routine sends an embedded MDF file in several events. See [specification CLASS1.PROTOCOL Type=35/36](http://www.vscp.org/docs/vscpspec/doku.php?id=class1.protocol#type_35_0x23_get_embedded_mdf)

Note that if sending the events back to back some devices will not be able to cope with the data stream. It is therefor advisable to have a short delay between each mdf data frame sent out.

##  vscp_goBootloaderMode

`<code=c>`
void vscp_goBootloaderMode(void);
`</code>`

This callback should force the system into boot loader mode according to the selected protocol.

##  vscp_getZone

`<code=c>`
uint8_t vscp_getZone(void);
`</code>`

Get zone for your device. Just return zero if not used.


##  vscp_getSubzone

`<code=c>`
uint8_t vscp_getSubzone(void);
`</code>`

Get subzone for your device. Just return zero if not used.

##  vscp_getFamilyCode

`<code=c>`
uint32_t vscp_getFamilyCode(void);
`</code>`

Not used at the moment. Return zero.

##  vscp_getFamilyType

`<code=c>`
uint32_t vscp_getFamilyType(void);
`</code>`

Not used at the moment. Return zero.

##  vscp_restoreDefaults

`<code=c>`
void vscp_restoreDefaults(void);
`</code>`

If 0x55/0xaa is written to standard register location 162 within one second defaults should be loaded by the device. This callback should restore device defaults.

##  vscp_hardreset

`<code=c>`
void vscp_hardreset(void);
`</code>`

Should be implemented only if **DROP_NICKNAME_EXTENDED_FEATURES** is defined.

Do a hard reset of the device.

##  vscp_wait_ms

`<code=c>`
void vscp_wait_ms(uint16_t ms);
`</code>`

Should be implemented only if **DROP_NICKNAME_EXTENDED_FEATURES** is defined.

Wait in a loop for ms milliseconds.

##  vscp_wait_s

`<code=c>`
void vscp_wait_s(uint16_t sec);
`</code>`

Should be implemented only if **DROP_NICKNAME_EXTENDED_FEATURES** is defined.

Wait in a loop for sec seconds.

----




\\ 
----
{{  ::copyright.png?600  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.grodansparadis.com">`Grodans Paradis AB`</a>``</p>``</HTML>`
