# Level II Callbacks

This is a list of all callbacks that you must handle in your level II application code.

The real functionality of the framework is set by how you handle the callbacks. Here we will go through each of them in turn.



## General callbacks

### vscp_frmw2_callback_get_milliseconds

```cpp
uint32_t
vscp_frmw2_callback_get_milliseconds(void* const puserdata);
```

Called by the framework to get the time in milliseconds.

#### Parameters:
*  **puserdata** Pointer to user data (typical points to context)
  
#### Return value
* unsigned integer that will get the time in milliseconds.

### vscp_frmw2_callback_get_timestamp

Called by the framework to get the time in microseconds. If your node does not have this timing resolution you can return the same value as for milliseconds times 1000.

```cpp
uint64_t
vscp_frmw2_callback_get_timestamp(void* const *puserdata)
```

#### Parameters
*  **puserdata** Pointer to user data (typical points to context)
  
#### Return value
* unsigned integer that will get the time in microseconds.

#### vscp_frmw2_callback_get_device_name

Get name of the device.  Used by the device capabilities report.

```cpp
int
vscp_frmw2_callback_get_device_name(void* const *puserdata, const char* pname)
```

#### Parameters
*  **puserdata** Pointer to user data (typical points to context)
*  **pname** Pointer to device name. The name should be no more than 64 characters including terminating zero.
  
#### Return value
* **VSCP_ERROR_SUCCESS** on success, or error code.

### vscp_frmw2_callback_read_reg

```cpp
int
vscp_frmw2_callback_read_reg(void* const puserdata, uint16_t page, uint32_t reg, uint8_t* pval);
```   
Read the value of one of the user registers. Valid register range is 0x00000000 - 0xffff0000.

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **page** Page to read from.
*  **reg** Register to read.
*  **pval** Pointer to variable that will get read value.
  
#### Return value
* **VSCP_ERROR_SUCCESS** on success, else error code. 
* **VSCP_ERROR_PARAMETER** should be returned if the register is invalid. 
* **VSCP_ERROR_INDEX_OOB** should be returned when trying to read a register that does not exist. This error is actually a warning. 

### vscp_frmw2_callback_write_reg

```cpp
int
vscp_frmw2_callback_write_reg(void* const /*puserdata*/, uint16_t page, uint32_t reg, uint8_t val);
```
Write value of one of the user registers. Valid register range is 0x00000000 - 0xffff0000.  
#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **page** Page to write to.
*  **reg** Register to write.
*  **val** Value to write.

#### Return value
* **VSCP_ERROR_SUCCESS** on success, else error code. 
* **VSCP_ERROR_PARAMETER** should be returned if the register is invalid. 
* **VSCP_ERROR_INDEX_OOB** should be returned when trying to read a register that does not exist. This error is actually a warning.   

### vscp_frmw2_callback_write_reg

```cpp
int
vscp_frmw2_callback_write_reg(void* const puserdata, uint16_t page, uint32_t reg, uint8_t val);
``` 

Write value of one of the user registers. Valid register range is 0x00000000 - 0xffff0000.

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **page** Page to write to.
*  **reg** Register to write.
*  **val** Value to write.
  
#### Return value
* **VSCP_ERROR_SUCCESS** on success, else error code. 
* **VSCP_ERROR_PARAMETER** should be returned if the register is invalid. 
* **VSCP_ERROR_INDEX_OOB** should be returned when trying to read a register that does not exist. This error is actually a warning.




### vscp_frmw2_callback_send_event_ex

```cpp
int
vscp_frmw2_callback_send_event_ex(void* const puserdata, vscpEventEx* pex);
``` 
#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **pex** Pointer to EventEx to send.

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, or error code.
*  **VSCP_ERROR_INVALID**_POINTER if pex is null.
*  **VSCP_ERROR_MEMORY** if there is not enough memory to send the event.

Send eventex to transport sublayer.

  - **ex** is copied in the callback.
  - if GUID is all zero then the GUID is set to the GUID of the node.
   
### vscp_frmw2_callback_enter_bootloader

```cpp
void
vscp_frmw2_callback_enter_bootloader(void* const puserdata);
```

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
  
### Return value
*  None.

Enter the bootloader mode of the device. **SHOULD (normally) NEVER RETURN!!!**
 

### vscp_frmw2_callback_set_event_time
  
```cpp  
int
vscp_frmw2_callback_set_event_time(void* const puserdata, vscpEventEx* pex);
``` 

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).  
*  **pex** Event to get info. 

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.    

Set all to zero or do nothing if not time information is available and the time values are already set to zero.

### vscp_frmw2_callback_init_persistent_storage

```cpp
int
vscp_frmw2_callback_init_persistent_storage(void* const puserdata);
```   

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.


Initialize persistent storage.


### vscp_frmw2_callback_enter_bootloader

```cpp
int
vscp_frmw2_callback_enter_bootloader(void* const puserdata, uint8_t* palgorithm);
``` 

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **palgorithm** Pointer to bootloader algorithm to use.

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.
  

Enter the bootloader mode of the device. Return the bootloader algorithm to use in *palgorithm. If there is only one algorithm it should be returned. If there are multiple algorithms the last used should be returned. If no bootloader is available return VSCP_ERROR_NOT_SUPPORTED.

### vscp_frmw2_callback_restore_defaults

```cpp
int
vscp_frmw2_callback_restore_defaults(void* const puserdata);
```

#### Parameters
*  **puserdata** Pointer to user data (typical points to context).

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.

Restore defaults

Called if 0x55/0xaa is written to register location 162 within one second defaults should be loaded by the device.

### vscp_frmw2_callback_stdreg_change

```cpp
int
vscp_frmw2_callback_stdreg_change(void* const puserdata, uint32_t stdreg);
``` 
#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **stdreg** Standard register that has changed.

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.
*  **VSCP_ERROR_INVALID**_POINTER if puserdata is null.
*  **VSCP_ERROR_MEMORY** if there is not enough memory to process the request.
*  **VSCP_ERROR_NOT_SUPPORTED** if the standard register is not supported.
*  **VSCP_ERROR_UNKNOWN** if the error is unknown.
*  
Notification received that standard register has been changed.

### vscp_frmw2_callback_feed_watchdog

```cpp
void
vscp_frmw2_callback_feed_watchdog(void* const puserdata);
```
#### Parameters
*  **puserdata** Pointer to user data (typical points to context).

#### Return value
*  None.

Feed the watchdog on systems that need to do
  that during lengthy operations.

#### vscp_frmw2_callback_reset

```cpp
void
vscp_frmw2_callback_reset(void* const puserdata);
```
#### Parameters
*  **puserdata** Pointer to user data (typical points to context).

#### Return value
*  None.
  
Reset the device.


### vscp_frmw2_callback_dm_action

```cpp
int
vscp_frmw2_callback_dm_action(void* const puserdata,
                              const vscpEventEx* const pex,
                              uint8_t action,
                              const uint8_t* const pparam)
``` 
#### Parameters
*  **puserdata** Pointer to user data (typical points to context).
*  **pex** Pointer to event that triggered the action.
*  **action** Action to perform.
*  **pparam** Pointer to action parameters (if any).

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.
*  **VSCP_ERROR_INVALID_POINTER** if puserdata or pex is null.
*  **VSCP_ERROR_MEMORY** if there is not enough memory to process the request.
*  **VSCP_ERROR_NOT_SUPPORTED** if the action is not supported.
*  **VSCP_ERROR_UNKNOWN** if the error is unknown.
  
Perform action defined in decision matrix.

## Bootloader callbacks

These callbacks are only used if you have a VSCP bootloader in your application. If you do not have a bootloader you can ignore these callbacks.

### vscpboot_init_hardware

```cpp
  int vscpboot_init_hardware(void)
``` 
#### Return value
*  VSCP_ERROR_SUCCESS on success, else error code.

Initialize hardware. This should be the first method called.The communication interface should be opened here and the system should be ready to receive firmware code events when done.

### vscpboot_goApplication

```cpp
void
vscpboot_goApplication(void);
```

Jump to application code.

Typically this is code like

```cpp
  void (*foo)(void) = (void (*)())0x12345678;
  foo();
```

where 0x12345678 is the address to jump to.

### vscpboot_run

```cpp
  int vscpboot_run(void)
``` 

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.

Check if the application has started.



### vscpboot_release_hardware

```cpp
  int vscpboot_release_hardware(void)
``` 
#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.

Free all used hardware resourcesand close communication interface.

### vscpboot_getBootFlag

```cpp
  uint8_t vscpboot_getBootFlag(void)
``` 
#### Return value
*  Bootflag for node. Zero return value means application should start. Nonzero return value means bootloader should start. Application can use other values for bootflag or multibyte storage as long as this routine report expected state.

Get Device bootflag from persistent storage.

### vscpboot_setBootFlag

```cpp
  int vscpboot_setBootFlag(uint8_t bootflag)
``` 
#### Parameters
*  **bootflag** New bootflag to set. Zero means application should start. Nonzero means bootloader should start.Application can use other values for bootflag or multi byte storage as long as this routine set proper state. 

#### Return value
*  **VSCP_ERROR_SUCCESS** on success, else error code.
  
Set Device bootflag in persistent storage. Application can use other values for bootflag or multibyte storage as long as this routine set proper state.

*  ### vscpboot_reboot

```cpp
  void vscpboot_reboot(void)
``` 
#### Return value
*  None.

Reboot the board. This method should never return.

### vscpboot_getConfig

```cpp
  vscpboot_config_t* vscpboot_getConfig(void)
```
#### Return value
*  Pointer to configuration structure.

Get pointer to configuration

### vscpboot_getGUID

```cpp
  uint8_t* vscpboot_getGUID(void)
```
#### Return value
*  Pointer to GUID.
  
Get GUID for device. On some devices GUID may be programmed by the firmware load process. This is typical for devices that have a id/userid in flash. For devices using Ethernet etc construct GUID in the usual manner. 

### vscpboot_isMemTypeValid

```cpp
  int vscpboot_isMemTypeValid(uint8_t type)
```
#### Parameters
*  **type** Memory type to check.
  
### Return value
*  Non zero if valid.
   
Check if memtype is valid. type == 0 is always valid

### vscpboot_isMemBankValid

```cpp
  int vscpboot_isMemBankValid(uint8_t bank)
```
#### Parameters
*  **bank** Memory bank to check.
  
### Return value
*  Non zero if valid.

Check if membank is valid. type == 0 is always valid

### vscpboot_programBlock

```cpp
  int vscpboot_programBlock(const uint8_t* pblock, uint8_t type, uint8_t bank)
```
#### Parameters
*  **pblock** Pointer to the block to program.
*  **type** Memory type.
*  **bank** Memory bank to program.
  
### Return value
*  **VSCP_ERROR_SUCCESS** on success and else error code.
  
Program a block

### vscpboot_calcPrgCrc

```cpp
  crc vscpboot_calcPrgCrc(void)
```
#### Return value
CRC-CCITT for programmed area. The CRC for the loaded data is calculated here. This is the CRC over all blocks programmed calculated with CRC-CCITT. For a successful programming this value should be the same as the one provided in the activate new image event.

### vscpboot_sendEventEx

```cpp
  int vscpboot_sendEventEx(vscpEventEx* pex)
```
#### Parameters
*  **pex** Pointer to VSCP event ex.
  
### Return value
*  **VSCP_ERROR_SUCCESS** on success.
  
  Send VSCP event ex

### vscpboot_getEventEx
```cpp
  int vscpboot_getEventEx(vscpEventEx* pex)
```
#### Parameters
*  **pex** Pointer to VSCP event ex structure.
  
### Return value
  * **VSCP_ERROR_SUCCESS** on success.
  
Should block until VSCP event is available.

**IMPORTANT!**
This routine should translate all VSCP_CLASS2_LEVEL1_PROTOCOL events to VSCP_CLASS1_PROTOCOL events.


[filename](./bottom_copyright.md ':include')