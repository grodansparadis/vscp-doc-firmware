# Alarms

The alarms byte in standard register space (128/0x80) is a register that should return non zero if the device is in or has been in an alarm condition. There are eight bits available for different alarms. All alarm bits should be set to zero when this register is read.

First of all nothing happens when you set a bit here more that that the bit is set to indicate an alarm. You probably should send an event also preferable an [alarm event](http://www.vscp.org/docs/vscpspec/doku.php?id=class1.alarm) when the alarm happens. The alarm register is there for later understanding that an alarm has occurred. The event for immediately inform that it's an alarm condition. 

If you want to make sure that the an alarm is acknowledged by receiving end add an action that does that and trigger on an event or use a read of the alarm register for it. If you implement something like that send alarms continuously with a delay between them (one second or something) until they are acknowledged. 

Just return zero when users read this register and your device have no alarms defined.

{% include "./bottom_copyright.md" %}
