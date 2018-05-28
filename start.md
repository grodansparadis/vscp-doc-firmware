# VSCP Firmware Specification

 

The VSCP firmware is code that you find in nodes. It is built to be able to fit in the smallest possible device and it is free of assumptions on which transport mechanism that is used even if currently most samples use CAN.

Anyway it is not a big deal to make a node that follow the the VSCP standard. You should however be aware of what [registers](http://www.vscp.org/docs/vscpspec/doku.php?id=register_abstraction_model) are, what [events](http://www.vscp.org/docs/vscpspec/doku.php?id=level_i_events) are, what a [MDF](http://www.vscp.org/docs/vscpspec/doku.php?id=module_description_file) is, what a [decision matrix (DM)](http://www.vscp.org/docs/vscpspec/doku.php?id=decision_matrix) is and what a [GUID](http://www.vscp.org/docs/vscpspec/doku.php?id=globally_unique_identifiers) is before you start. You find all that information in the [VSCP Specification](http://www.vscp.org/docs/vscpspec/doku.php).

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid@vscp.org](guid@vscp.org) providing your contact information and ask for a series. You get a series free of charge.

## Steps

[Introduction](Introduction) \\  
[A millisecond clock](A millisecond clock) \\  
[The init button](The init button) \\ 
[The status LED](The status LED) \\
[Node startup](Node startup) \\
[The main loop](The main loop) \\
[CAN or not CAN](CAN or not CAN) \\
[Registers and the MDF](Registers and the MDF) \\
[Abstractions](Abstractions) \\ 
[Callbacks](Callbacks) \\ 
[The decision matrix](The decision matrix) \\
[Alarms](Alarms) \\
[Test your implementation](Test your implementation)

\\ 
----
{{  ::copyright.png?600  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.grodansparadis.com">`Grodans Paradis AB`</a>``</p>``</HTML>`

