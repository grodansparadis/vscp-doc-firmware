# The Init. button

A VSCP node should have an init. button that when pressed makes the device part of the network it's on. This process called the nickname discovery process is visualized by a [status led](the_status_led) that is blinking during the init. process and then lights steady when it's finished.

To support this process a routine to handle a button press is therefore needed. In the Paris reference project this code is in two places. In  the interrupt routine where it looks like this

```cpp
// Check for init button
if ( INIT_BUTTON ) {
    vscp_initbtncnt = 0;
} 
else {
    // Active
    vscp_initbtncnt++;
}
```

which just increase the **vscp_initbtncnt** if the button is pressed and set it to zero if released. Most people recognize this as a typical denounce code for a button.

The action for the button, which is placed in the main loop, that is what happens when it has been pressed for more than 250 milliseconds look like this

```cpp
if ( ( vscp_initbtncnt > 250 ) &&
          ( VSCP_STATE_INIT != vscp_node_state ) ) {
    // Init button pressed
    vscp_nickname = VSCP_ADDRESS_FREE;
    writeEEPROM( VSCP_EEPROM_NICKNAME, VSCP_ADDRESS_FREE );
    vscp_init();
}
```

What is happening here is that the nickname of the node is set to the free nickname (**0xff**) and the EEPROM storage for it is also reseted and then the **vscp_init()** method is called which started the nickname discovery process.

The check for not being in the VSCP_STATE_INIT state prevent this from happening again an again and again if a user holds the button down.


[filename](./bottom_copyright.md ':include')
