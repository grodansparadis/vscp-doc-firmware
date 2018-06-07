# Summary

## Introduction

* [About this document](README.md)
* [VSCP - what is it?](what_is_vscp.md)

## Level I nodes

* [Level I](level1_specifics.md)
* [Intro](level1_introduction.md)
* [A millisecond clock](level1_a_millisecond_clock.md)
* [The init button](level1_the_init_button.md)
* [The status LED](level1_the_status_led.md)
* [Node startup](level1_node_startup.md)
* [The main loop](level1_the_main_loop.md)
* [CAN or not CAN](level1_can_or_not_can.md)
* [Registers and the MDF](level1_registers_and_the_mdf.md)
* [Abstractions](level1_abstractions.md)
* [Callbacks](./level1_callbacks.md)
    - [getVSCPFrame](./level1_callbacks.md#getvscpframe)
    - [sendVSCPFrame](./level1_callbacks.md#sendvscpframe)
    - [vscp_getMajorVersion](./level1_callbacks.md#vscp_getmajorversion)
    - [vscp_getMinorVersion](./level1_callbacks.md#vscp_getminorversion)
    - [vscp_getSubMinorVersion](./level1_callbacks.md#vscp_getsubminorversion)
    - [vscp_getGUID](./level1_callbacks.md#vscp_getguid)
    - [vscp_setGUID](./level1_callbacks.md#vscp_setguid)
    - [vscp_getUserID](./level1_callbacks.md#vscp_getuserid)
    - [vscp_setUserID](./level1_callbacks.md#vscp_setuserid)
    - [vscp_getManufacturerId](./level1_callbacks.md#vscp_getmanufacturerid)
    - [vscp_setManufacturerId](./level1_callbacks.md#vscp_setmanufacturerid)
    - [vscp_getBootLoaderAlgorithm](./level1_callbacks.md#vscp_getbootloaderalgorithm)
    - [vscp_getBufferSize](./level1_callbacks.md#vscp_getbuffersize)
    - [getRegisterPagesUsed](./level1_callbacks.md#getregisterpagesused)
    - [vscp_getMDF_URL](./level1_callbacks.md#vscp_getmdf_url)
    - [vscp_readNicknamePermanent](./level1_callbacks.md#vscp_readnicknamepermanent)
    - [vscp_writeNicknamePermanent](./level1_callbacks.md#vscp_writenicknamepermanent)
    - [vscp_init_pstorage](./level1_callbacks.md#vscp_init_pstorage)
    - [vscp_getControlByte](./level1_callbacks.md#vscp_getcontrolbyte)
    - [vscp_setControlByte](./level1_callbacks.md#vscp_setcontrolbyte)
    - [vscp_getPageSelect](./level1_callbacks.md#vscp_getpageselect)
    - [vscp_setPageSelect](./level1_callbacks.md#vscp_setpageselect)
    - [vscp_readAppReg](./level1_callbacks.md#vscp_readappreg)
    - [vscp_writeAppReg](./level1_callbacks.md#vscp_writeappreg)
    - [vscp_getMatrixInfo](./level1_callbacks.md#vscp_getmatrixinfo)
    - [vscp_getEmbeddedMdfInfo](./level1_callbacks.md#vscp_getembeddedmdfinfo)
    - [vscp_goBootloaderMode](./level1_callbacks.md#vscp_gobootloadermode)
    - [vscp_getZone](./level1_callbacks.md#vscp_getzone)
    - [vscp_getSubzone](./level1_callbacks.md#vscp_getsubzone)
    - [vscp_getFamilyCode](./level1_callbacks.md#vscp_getfamilycode)
    - [vscp_getFamilyType](./level1_callbacks.md#vscp_getfamilytype)
    - [vscp_restoreDefaults](./level1_callbacks.md#vscp_restoredefaults)
    - [vscp_hardreset](./level1_callbacks.md#vscp_hardreset)
    - [vscp_wait_ms](./level1_callbacks.md#vscp_wait_ms)
    - [vscp_wait_s](./level1_callbacks.md#vscp_wait_s)		
* [The decision matrix](level1_decision_matrix.md)
* [Alarms](level1_alarms.md)
* [Test your implementation](level1_test_your_implementation.md)
* [Real world examples](level1_example_devices.md)

## Level II nodes
* [Start](level2_start.md)
* [Introduction](level2_introduction.md)

## Appendix

*  [Firmware specification history](firmware_specification_history.md)

## Other documentation

*  [VSCP Daemon](https://grodansparadis.gitbooks.io/the-vscp-specification)
*  [VSCP Helper lib](https://grodansparadis.gitbooks.io/the-vscp-helper-library)
*  [VSCP Works](https://www.vscp.org/docs/vscpworks/doku.php?id=start)
*  [VSCP L1 Framework](https://github.com/BlueAndi/vscp-framework/blob/master/README.md)
*  [VSCP Arduino](https://github.com/BlueAndi/vscp-arduino)
*  [VSCP Javascript lib.](https://grodansparadis.gitbooks.io/the-vscp-javascript-library/)
*  [VSCP HTML demo](https://www.vscp.org/docs/html5/doku.php)
*  [General VSCP wiki](https://www.vscp.org/wiki/doku.php)
