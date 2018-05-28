# The VSCP Firmware Specification

![VSCP logo](./images/logo_100.png)

**Specification reversion:** 1.1 - 2018-05-29 

This document is the official VSCP (Very Simple Control Protocol) firmware specification. 

VSCP is an IoT framework mainly built for resource limited devices. This document describes firmware code support that is freely available for developers of VSCP compatible hardware.

Apart from going through this document You can read all about and download VSCP from [the VSCP site](https://www.vscp.org "The VSCP site"). 

**VSCP is free.** Free to use. Free to change. Free to do whatever you want to do with it. VSCP is not owned by anyone. VSCP will stay free and gratis forever.

Author 2000-2018 Åke Hedman, [Grodans Paradis AB](https://www.grodansparadis.com), [akhe@grodansparadis.com](akhe@grodansparadis.com)  

This document is licensed under [Creative Commons BY 4.0](https://creativecommons.org/licenses/by/4.0/) and can be freely copied, redistributed, remixed, transformed, built upon as long as you give credits to the author.

If you use VSCP please consider contributing resources or time to the project ([http://vscp.org/support.php](http://vscp.org/support.php)). 

# Abstract

The VSCP firmware is code that you find in harware nodes. It is built to be able to fit in the smallest possible device and it is free of assumptions on which transport mechanism that is used even if currently most samples use CAN-BUS.

It is not a big deal to make a node that follow the the VSCP standard. You should however be aware of what [registers](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_register_abstraction_model.html) are, what [events](https://grodansparadis.gitbooks.io/the-vscp-specification/content/introduction.html) are, what a [Module Description file (MDF)](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_module_description_file.html) is, what a [decision matrix (DM)](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_decision_matrix.html) is and what a [GUID](https://grodansparadis.gitbooks.io/the-vscp-specification/content/vscp_globally_unique_identifiers.html) is before you start. You find all that information in the VSCP Specification.

If you are making modules for sale or delivery outside your own lab bench you should probably ask for a GUID series for your device. Send a mail to [guid-request@vscp.org](guid-request@vscp.org) providing your contact information and ask for a series. You get a series free of charge.


 
{% include "./bottom_copyright.md" %}



