# Remote variables

Remote variables, also called "abstractions", are described [here](http://docs.vscp.org/spec/latest/#/./vscp_module_description_file).

As we saw when the registers where set a 16-bit register needs two consecutive registers to be stored in register space. This is workable for a machine to handle but humans should not have to cope with things like that. The solution to this problem is **remote variables**

Remote variables should be thought of as being on a level above registers. While registers all are 8-bit values, remote variables can be strings, floating point values or signed sixteen bit integers as we need for our example. 

If you just have eight bit values or less you don't need remote variables. Just don't add any. The VSCP framework will figure out itself where to use remote variables or registers. 

The remote variables for our two temperature values will look like this

```xml
`<remotevars>`

    // Abstraction for temperature value 1
    <remotevar 
            id="temp1" 
            type="int16_t" 
            default="0" 
            page = "0" 
            offset = "10"  > 
        `<name lang="en">`temperature1`</name>`     
        `<description lang="en">`Sensor reading for temperature 1`</description>`         
        `<help type="text/url"  lang="en">`some help url`</help>`     
        `<access>`r`</access>`     
    `</remotevar>`

    // Abstraction for temperature value 2
    <remotevar 
            id="temp2" 
            type="int16_t" 
            default="0" 
            page = "0" 
            offset = "12"  > 
        `<name lang="en">`temperature2`</name>`     
        `<description lang="en">`Sensor reading for temperature 2`</description>`         
        `<help type="text/url"  lang="en">`some help url`</help>`     
       `<access>`r`</access>`     
    `</remotevar>`
    
`</remotevars>`    

```

After this has been done an end user accessing your device will instead of a high and a low 8-bit value for the temperature see and work with a signed 16-bit integers. In VSCP Works you can see and handle the remote variables in the remote variable tab of the configuration window. In a setup wizard you can handle settings even easier than that.


[filename](./bottom_copyright.md ':include')
