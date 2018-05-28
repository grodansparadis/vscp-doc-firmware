# Node startup

When a node is started up the first time it should get a nickname, that is act like the init. button has been pressed.

On the Paris node reference project this code looks like this

`<code=c>`
init(); // Initialize Micro controller

// Check VSCP persistent storage and
// restore if needed
if ( !vscp_check_pstorage() ) {
    // Spoiled or not initialized - reinitialize
    init_app_eeprom();
    init_app_ram(); // Needed because some ram positions
                    // are initialized from EEPROM
}
vscp_init(); // Initialize the VSCP functionality
`</code>`

Here the system is initialized in the **init()** method and then the **vscp_check_pstorage()** method is called which checks if initialization of the VSCP system is needed or not.  FALSE is returned if initialization is needed and then default values are written to persistent storage and also to ram in this case as some values for this node is fetched from EEPROM.

After this the **vscp_init** method is called which is the main VSCP initialization method and then the main loop is entered.

Note that there can be other causes to restore default values. Some want to check a checksum over persistent storage and so on. But this is typically what is needed as a minimum.


\\ 
----
{{  ::copyright.png?600  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.grodansparadis.com">`Grodans Paradis AB`</a>``</p>``</HTML>`
