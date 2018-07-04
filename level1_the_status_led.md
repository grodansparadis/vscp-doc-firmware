# The Status LED

The status LED should blink on a node when it is looking for a nickname and then light steady when it is done.

For the [Paris node reference project](https://www.grodansparadis.com/paris/manual/doku.php?id=start) this is all handled in the interrupt routine

```cpp
// Status LED
vscp_statuscnt++;
if ( ( VSCP_LED_BLINK1 == vscp_initledfunc ) &&
        ( vscp_statuscnt > 100 ) ) {
    if ( STATUS_LED ) {
        STATUS_LED = 0;
    }
    else {
        STATUS_LED = 1;
    }
    vscp_statuscnt = 0;
}
else if (VSCP_LED_ON == vscp_initledfunc) {
    STATUS_LED = 1;
    vscp_statuscnt = 0;
}
else if (VSCP_LED_OFF == vscp_initledfunc) {
    STATUS_LED = 0;
    vscp_statuscnt = 0;
}
```

This code should not be hard to follow. 

{% include "./bottom_copyright.md" %}

