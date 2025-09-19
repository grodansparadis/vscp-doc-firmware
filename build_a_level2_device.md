# Building a Level II Device

To build a VSCP Level II device you typically need a more capable microcontroller or a small embedded computer. A good choice of microcontroller is a 32-bit ARM Cortex-M series microcontroller with at least 64 kB of flash memory and 8 kB of RAM. If the device has a CAN interface it is a possible way to connect the device to the rest of the world. But the VSCP protocol is independent of the physical layer so you can use any other physical layer if you want.

You can also use a small embedded computer like a Raspberry Pi or an ESP32. These devices have more processing power and memory than a typical microcontroller and are easy to program using free tools.

To get started you can download the VSCP Level II reference implementation from [github](https://github.com/grodansparadis/vscp_firmware). This code is written for a 32-bit ARM Cortex-M series microcontroller but can be ported to other microcontrollers with some effort. The code is written in C and is easy to understand. It is also well documented so you can learn a lot by reading the code. The code is released under the MIT license so you can use it in your own projects without any restrictions.

## Step 1
First add the file [vscp-firmware-level2.h](https://github.com/grodansparadis/vscp-firmware/blob/master/common/vscp-firmware-level2.h) and [vscp-firmware-level2.c](https://github.com/grodansparadis/vscp-firmware/blob/master/common/vscp-firmware-level2.c) from the vscp-firmware repository to your project.

The best way to do this is to create a folder called **third-party** in your project and check out the [vscp-firmware](https://github.com/grodansparadis/vscp-firmware) repository as a submodule there. Then include the files *third-party/vscp-firmware/common/vscp-firmware-level2.c* and *third-party/vscp-firmware/common/vscp-firmware-helper.c* in your build. 

The vscp-firmware-level2.c implements the protocol functionality and the file depends on some helper functions that are implemented in the file *vscp-firmware-helper.c*.

## Step 2
Define the `vscp_frmw2_config` configuration structure defined in vscp-firmware-level2.h. This structure contains the configuration for the firmware. 

You need to set the following members after initializing the structure to zero:  

* **m_level** - Set this to **VSCP_LEVEL_2** to indicate that this is a Level II device.
* **m_puserdata** - This is a pointer to user data that is passed to the callbacks. You can set this to NULL if you do not need it.
* **m_interval_heartbeat** - This is the interval in milliseconds between heartbeat events. Set to 0 to disable heartbeat events.
* **m_interval_caps** - This is the interval in milliseconds between capability events. Set to 0 to disable capability events.
* **m_pDm** - Set this to a pointer to the decision matrix storage. You can set this to NULL if you do not have a decision matrix. There are some macros defined in vscp-firmware-level2.h to help you handle decision matrix items from this base address.
* **m_nDmRows** - This is the number of rows in the decision matrix. Set to 0 if you do not have a decision matrix.
* **m_sizeDmRow** - This is the size of each row in the decision matrix. Set to 0 if you do not have a decision matrix.
* **m_regOffsetDm** - This is the register offset for the decision matrix. Normally set to zero.
* **m_pageDm** - This is the page number for the decision matrix. Normally set to zero.
* **m_pInternalMdf** - This is a pointer to the internal MDF if your device stores it locally. You set this to NULL if your MDF is stored externally.
* **m_pEventsOfInterest** - This is a pointer to an array of events that the device is interested in receiving. Normally you set this to NULL if you do not have any specific events of interest.
* **m_index** - Index for your device. Typically this is also a register in your device.
* **m_subzone** - Subzone for your device. Typically this is also a register in your device.
* **m_zone** - Zone for your device. Typically this is also a register in your device.


## Step 3
You need to implement a millisecond clock source. This is used by the firmware for timing and for timeouts. Better still if you can find a microsecond clock source for better resolution. 

## Step 4
Then implement the required callbacks to interface the firmware with your hardware. You can find more information about the required callbacks in the file vscp-firmware-level2.h in the **callbacks** section.

* **vscp_frmw2_callback_get_milliseconds** - This callback should return the number of milliseconds since the system started. If you have a microsecond clock source, you can use that and divide by 1000 to get milliseconds.
  
* **vscp_frmw2_callback_get_milliseconds** - This callback should return the number of microseconds since the system started. If you do not have a microsecond clock source, you can return milliseconds * 1000. The resolution will be lower but it will work.

* **vscp_frmw2_callback_set_event_time** - Set the time information of the supplied event (timestamp, year, month, day, hour, minute, second). Set to zero if not available. Setting the values to zero means that the next VSCP interface this event pass will set the time information.

* **vscp_frmw2_callback_feed_watchdog** - This callback should feed the watchdog timer. If you do not have a watchdog timer, you can just return from the callback.

* **vscp_frmw2_callback_reset** - This callback should reset the device (warm start). How this is done depends on your hardware. If you do not have a way to reset the device, you can just return from the callback.

* **vscp_frmw2_callback_get_ip_addr** - This callback should return the IP address (v4 or v6) of the device in a 16 byte wide buffer. The buffer should be filled with the IP address in big-endian format.

* **vscp_frmw2_callback_restore_defaults** - This callback should restore the device to its default state. This typically means resetting all configuration parameters to their default values. How this is done depends on your hardware and configuration storage.

* **vscp_frmw2_callback_send_event_ex** - This callback should send an event to the VSCP network. The event is passed as a pointer to a `vscpEventEx` structure (defined in [vscp.h](https://github.com/grodansparadis/vscp-firmware/blob/master/common/vscp.h)). You need to implement the code to send the event over your chosen communication interface.

* **vscp_frmw2_callback_enter_bootloader** - This callback should put the device into bootloader mode. This is typically done by triggering a hardware reset and entering a special bootloader mode. How this is done depends on your hardware and bootloader. If no bootloader is available, you can reset the device or just return from the callback. 

* **vscp_frmw2_callback_dm_action** - This callback is called when a decision matrix action is triggered. The action number is passed as a parameter. You need to implement the code to handle the action. The action number is 0-based so the first action is action 0. If your device does not have a decision matrix, you can just return from the callback with **VSCP_ERROR_SUCCESS**.

* **vscp_frmw2_callback_segment_ctrl_heartbeat** - This callback is called when a segment controller heartbeat is received. 

* **vscp_frmw2_callback_report_events_of_interest** - Report events you are interested in receiving. This is used by the firmware to filter events. You need to implement the code to report the events you are interested in. If your device does not support this, you can just return from the callback with **VSCP_ERROR_SUCCESS**.

* **vscp_frmw2_callback_read_reg** - This callback should read a VSCP register from the device. The register address is passed as a parameter. You need to implement the code to read the register and return the value in the `pvalue` parameter. 

* **vscp_frmw2_callback_write_reg** - This callback should write a VSCP register to the device. The register address and value are passed as parameters. You need to implement the code to write the register. 

* **vscp_frmw2_callback_stdreg_change** - This callback is called when a standard VSCP register is changed. The register address and new value are passed as parameters. If your cpde depends on standard registers, you can implement the code to handle the change. If not, you can just return from the callback with **VSCP_ERROR_SUCCESS**.

## Step 5
Somewhere in your code after you have initiated the system call the function `vscp_frmw2_init` to initialize the VSCP Level II firmware. This function takes a pointer to a `vscp_frmw2_config` structure as a parameter. This structure contains the configuration for the firmware.

## Step 6
In your main loop call the function `vscp_frmw2_work` when you receive events  and periodically also when not (event pointer set to NULL). This function processes the VSCP Level II firmware. It should be called as often as possible to ensure that the firmware works correctly.

Thats it, now you have a basic VSCP Level II device. You can now start adding functionality to your device by implementing the decision matrix and handling events. Use [VSCP Works](https://github.com/grodansparadis/vscp-works-qt) to debug and work with your new device.

[filename](./bottom_copyright.md ':include')