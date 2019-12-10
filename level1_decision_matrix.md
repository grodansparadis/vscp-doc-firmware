# The decision matrix

Do you need a [decision matrix](http://docs.vscp.org/spec/latest/#/./vscp_decision_matrix) for for your new device? As always you are the only one who knows. If your device should react in some way on incoming event on the bus the answer is usually yes. The decision matrix gives your users flexibility. Yes you can hard code whats happen if events come into your device. That can work well for you. As said above it's your decision. We recommend that you implement one in your device.

Your first decision when you create a decision matrix is the number of rows it should have. Each row is eight bytes. So do as many as you have room for. 

The next is to decide actions and action parameters. That is what can be done when a specific event is received.  A typical action is "turn on the relay that is specified by the action parameter" and another a corresponding turn off. Maybe another version that can turn on/turn off several relays at the same time with an action parameter with bits set for relays that should adhere to the request. You may want to send an event when another event comes in. Oh well I'm sure you got it.

Events that trigger an action is set in class and type filter and masks. This construction allow several events to trigger one decision matrix row. The easiest case is one event. Set the event in the filter part and set the mask to all bit cares that is all ones.

You can also check for origin of event and zone/subzone if you want. You have it all described fully [here](http://docs.vscp.org/spec/latest/#/./vscp_decision_matrix).

VSCP events can contain a lot of different kinds of data. Why are there no options to trigger on them all? Well you have to stop somewhere and we decided on origin, zone and subzone. If you want more compares add actions and corresponding registers that check for them. If for example you want an action that check for the index byte of an event or the sensor index of a measurement set apart a register for that index and make an action that compare the index register you created with the incoming event before the action is carried out. There is no limits on what you can do in this.

Below you see the decision matrix parser for the Paris reference module. The elements of the matrix is stored in EEPROM and the current event is in the **imsg** structure.

If you look at the code you see the comment just before the switch OK "Trigger this action". This is the place you change to add your own actions. The rest should be possible to copy right of if you change the EEPROM read to your own and the decision matrix location to your own.

The action routines is just methods like any other methods in your code.

```cpp
void doDM(void)
{
    unsigned char i;
    unsigned char dmflags;
    unsigned short class_filter;
    unsigned short class_mask;
    unsigned char type_filter;
    unsigned char type_mask;

    // Don't deal with the protocol functionality
    if ( VSCP_CLASS1_PROTOCOL == vscp_imsg.vscp_class ) return;

    for (i = 0; i < DESCION_MATRIX_ROWS; i++) {

        // Get DM flags for this row
        dmflags = readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + 1 + (8 * i) );

        // Is the DM row enabled?
        if ( dmflags & VSCP_DM_FLAG_ENABLED ) {

            // Should the originating id be checked and if so is it the same?
            if ( ( dmflags & VSCP_DM_FLAG_CHECK_OADDR ) &&
                    ( vscp_imsg.oaddr != readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) ) ) ) {
                continue;
            }
            
            // Check if zone should match and if so if it match
            if ( dmflags & VSCP_DM_FLAG_CHECK_ZONE ) {
                if ( 255 != vscp_imsg.data[ 1 ] ) {
                    if ( vscp_imsg.data[ 1 ] != readEEPROM( VSCP_EEPROM_END + REG_RELAY_ZONE ) ) {
                        continue;
                    }
                }
            }
            
            class_filter = ( dmflags & VSCP_DM_FLAG_CLASS_FILTER)*256 +
                    readEEPROM( VSCP_EEPROM_END +
                    REG_DESCION_MATRIX +
                    (8 * i) +
                    VSCP_DM_POS_CLASSFILTER);
            class_mask = ( dmflags & VSCP_DM_FLAG_CLASS_MASK)*256 +
                    readEEPROM( VSCP_EEPROM_END +
                    REG_DESCION_MATRIX +
                    (8 * i) +
                    VSCP_DM_POS_CLASSMASK);
            type_filter = readEEPROM( VSCP_EEPROM_END +
                    REG_DESCION_MATRIX +
                    (8 * i) +
                    VSCP_DM_POS_TYPEFILTER);
            type_mask = readEEPROM( VSCP_EEPROM_END +
                    REG_DESCION_MATRIX +
                    (8 * i) +
                    VSCP_DM_POS_TYPEMASK);

            if ( !( ( class_filter ^ vscp_imsg.vscp_class ) & class_mask ) &&
                    !( ( type_filter ^ vscp_imsg.vscp_type ) & type_mask ) ) {

                // OK Trigger this action
                switch ( readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTION ) ) {

                    case ACTION_ON: // Enable relays in arg. bitarry
                        doActionOn( dmflags, readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTIONPARAM ) );
                        break;

                    case ACTION_OFF: // Disable relays in arg. bitarry
                        doActionOff( dmflags, readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTIONPARAM ) );
                        break;

                    case ACTION_PULSE: // Pulse relays in arg. bitarry, zone, subzone
                        doActionPulse( dmflags, readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTIONPARAM ) );
                        break;

                    case ACTION_STATUS: // Send status for all relays
                        doActionStatus( dmflags, readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTIONPARAM ) );
                        break;

                    case ACTION_DISABLE: // Disable realys in bitarray
                        doActionDisable( dmflags, readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTIONPARAM ) );
                        break;

                    case ACTION_TOGGLE: // Toggle relay(s)
                        doActionToggle( dmflags, readEEPROM( VSCP_EEPROM_END + REG_DESCION_MATRIX + (8 * i) + VSCP_DM_POS_ACTIONPARAM ) );
                        break;

                } // case
            } // Filter/mask
        } // Row enabled
    } // for each row
}
```


[filename](./bottom_copyright.md ':include')
