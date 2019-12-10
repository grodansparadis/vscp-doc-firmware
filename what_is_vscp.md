# What is VSCP?

VSCP stands for Very Simple Control Protocol and was initially constructed in year 2000 as a sensor and control protocol that worked over RS-485 and CAN-BUS. Since then VSCP has grown to a full IoT framework with both firmware and software, user interface tools and development tools for sensor and control (IoT) solutions.

VSCP has been called the IoT protocol that was developed before IoT was born and VSCP is still the most complete solution available. 

The VSCP firmware is code that you find in harware nodes. It is built to be able to fit in the smallest possible device and it is free of assumptions on which transport mechanism that is used even if currently most samples use CAN-BUS.

It is not a big deal to make a node that follow the the VSCP standard. You should however be aware of what [registers](http://docs.vscp.org/spec/latest/#/./vscp_register_abstraction_model) are, what [events](http://docs.vscp.org/spec/latest/#/./level_i_events) are, what a [Module Description file (MDF)](http://docs.vscp.org/spec/latest/#/./vscp_module_description_file) is, what a [decision matrix (DM)](http://docs.vscp.org/spec/latest/#/./vscp_decision_matrix) is and what a [GUID](http://docs.vscp.org/spec/latest/#/./vscp_globally_unique_identifiers) is before you start. You find all this information in the [VSCP Specification](http://docs.vscp.org/spec/latest).

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid-request@vscp.org](guid-request@vscp.org) providing your contact information and ask for a series. You get a series free of charge.

[filename](./bottom_copyright.md ':include')