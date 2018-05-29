# A millisecond clock

The VSCPP firmware code rely on a millisecond clock. Therefore your first task is to add this to your code and make sure that it works. In the Paris reference design you find this code in the [interrupt routine around line 133](https://github.com/grodansparadis/can4vscp_paris/blob/master/main.c). It looks like this 

```cpp
// Clock
if ( INTCONbits.TMR0IF ) { // If a Timer0 Interrupt then...
  // Reload value for 1 ms reolution
  WriteTimer0( TIMER0_RELOAD_VALUE );
  vscp_timer++;
  vscp_configtimer++;
  measurement_clock++;
  timeout_clock++;
}
```

**vscp_timer** and **vscp_configtimer** is the important things here. They are both defined by the firmware code and should be increased by on every millisecond, so that is what we do in our code.

The usual way, as used here, to create a clock is to load a value to a timer and then let it interrupt the system when the value has been counted down and then load it again and repeat the process.  There may be other possibility available on your system.

You may ask why we don't make a call into the firmware code to update the variables. This is because we want this code to be as small as possible and use as little stack space as possible. **Size matters.** Flash space is for the application not for the framework you use.


{% include "./bottom_copyright.md" %}
