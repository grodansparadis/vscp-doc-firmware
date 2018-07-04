# The Main loop

The VSCP related main loop for the [Paris reference node](https://www.grodansparadis.com/paris/manual/doku.php?id=start) look like this

```cpp
while ( 1 ) {   // Loop Forever

   ClrWdt();   // Feed the dog

   if ( ( vscp_initbtncnt > 250 ) &&
                ( VSCP_STATE_INIT != vscp_node_state ) ) {

       // Init button pressed
       vscp_nickname = VSCP_ADDRESS_FREE;
       writeEEPROM( VSCP_EEPROM_NICKNAME, VSCP_ADDRESS_FREE );
       vscp_init();
            
    }

    // Check for a valid  event
    vscp_imsg.flags = 0;
    vscp_getEvent();

    switch ( vscp_node_state ) {

        // Cold/warm reset
        case VSCP_STATE_STARTUP: 

            // Get nickname from EEPROM
            if (VSCP_ADDRESS_FREE == vscp_nickname) {
                // new on segment need a nickname
                vscp_node_state = VSCP_STATE_INIT;
            } 
            else {
                // been here before - go on
                vscp_node_state = VSCP_STATE_ACTIVE;
                vscp_goActiveState();
            }
            break;

        // Assigning nickname
        case VSCP_STATE_INIT: 
            vscp_handleProbeState();
            break;

        // Waiting for host initialisation
        case VSCP_STATE_PREACTIVE:  
            vscp_goActiveState();
            break;

        // The normal state
        case VSCP_STATE_ACTIVE:     

            // Check for incoming event?
            if (vscp_imsg.flags & VSCP_VALID_MSG) {

                if ( VSCP_CLASS1_PROTOCOL == vscp_imsg.vscp_class  ) {

                    // Handle protocol event
                    vscp_handleProtocolEvent();

                }

                doDM();
					
            }
            break;

        // Everything is *very* *very* bad.
        case VSCP_STATE_ERROR: 
            vscp_error();
            break;

        default: // Should not be here...
            vscp_node_state = VSCP_STATE_STARTUP;
            break;

    }
    
    ...
    do other things here
    ...
}
```

We recognize the button function we looked at before. An it is followed by

```cpp
// Check for a valid  event
vscp_imsg.flags = 0;
vscp_getEvent();
```

which fetch one VSCP event. This event is placed in the vscp_imsg structure if one is available and then the flag **VSCP_VALID_MSG** s set in vscp_imsg.flags.

If you look at the code for the Paris reference node you will see that the method **vscp_getEvent** just read a CAN message and translate it into a VSCP event and place it in the global vscp_imsg structure. This is because we use the buffering supplied by the processor in this case. If you don't have that buffering you should probably implement it in your firmware. Anyway the global vscp_imsg is there to make it possible to build systems with low memory footprint and grow from there when needed.

When a possible incoming event is fetched we have a case that goes through the VSCP states. 

#  VSCP_STATE_STARTUP

This is the state we are in after a cold or a warm reset. From here we go to the **VSCP_STATE_INIT** if a nickname has not been assigned to this node or to the **VSCP_STATE_ACTIVE** if a nickname is assigned. 

This means that a node that is started for the first time will go to nickname discovery while a node that has been started before and which has got a nickname will go into the actice state ready to do it's work.

# VSCP_STATE_INIT

In the init. state we do one thing. Call the **vscp_handleProbeState** which is the handler for the nickname discover process. This method will take us out of this state either when a nickname is found or not.

# VSCP_STATE_PREACTIVE

In this state we do one thing, call the method **vscp_goActiveState** which report to the work that we are here and have found a free nickname id by sending the event **CLASS1.EVENT, Type=2, New node on line** which is the same as the probe send to look for a free nickaname execpt that the origin is the discovered free nickname and not 0xff.

# VSCP_STATE_ERROR

You should not get to this state but if you do everything is bad. 

# VSCP_STATE_ACTIVE

In the active state two things needs to be done. First if a protocol event is received it must be feed to the vscp firmware code and this is what is done in the method **vscp_handleProtocolEvent** then if the node have a decision matrix is the event should be feed through it to search for a possible match. This is done in the method **doDM** which is not part of the vscp firmware code. 

That's it, oh well almost. 

{% include "./bottom_copyright.md" %}
