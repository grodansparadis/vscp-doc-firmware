# Test your implementation

When you have coded your system you probably want to make sure that it works properly. The following steps is a way to do this

 1.  Check that you code runs. Set a break point at the main location.
 2.  Check the that 1 millisecond clock works. The example code have a method **DoOneSecondWork** and you can toggle a pin here and verify that i toggles every second.
 3.  Connect you device to the bus and press the init. button to see if it send some events. Maybe even better is to add a send event line code to you code after everything is initialized.
 4.  Check that your node can receive events. Best way to do this is to set a break point in the [getVSCPFrame](./level1_callbacks.md#getvscpframe) or toggle a pin also here.
 5.  If all this works start VSCP works and verify that you unit is found in the scan function.
 6.  Last go to the configuration functionality of VSCP Works and verify that it works. Test that you can change the values in all rw values etc.
 7.  Test your devices functionality.


[filename](./bottom_copyright.md ':include')
