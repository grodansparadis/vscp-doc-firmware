# Registers and the MDF

The register abstraction model is fully described [here](http://docs.vscp.org/spec/latest/#/./vscp_register_abstraction_model).

Before implementing all the [callbacks](./level1_callbacks.md) that lay out the functionality of your module you should start to think of what registers to implement, where they should be, there values etc. 

The standard register set is defined by callbacks so you don't have to worry about them at this stage. Just the application registers are of interest here.

You don't have to add all registers at once. Start with a few and add others as need arise. Maybe your device does not have registers at all. Just skip them. 

First of all registers are 8-bits. So if you have a value that is 16-bits you need to use two registers to lay it out in register space of your device. The byte order should always be HIGH byte before LOW byte that is the higher byte should be stored first, that is in big endian order.

Let us look at an example. Suppose we have two signed 16-bit values that represent temperature readings from a sensor times 100.

We lay these application registers out with start at offset=10 at page=0. This is just something we select because we like it. The mapping we do is entirely up to us it is just logical and have nothing to do with how things physically is laid out on your device. You can scatter registers around in register space or have them laid out after each other. It's up to you. Here is what we do here


*  The first temperature value (temperature1) will then be in registers 10 and 11 and register 10 will hold the most significant byte.

*  The second temperature value (temperature2) will then be in registers 12 and 13 and register 12 will hold the most significant byte.

We also add two registers that tell how many seconds it should be between temperature readings. Just one byte is needed for each of them and we but them on page=0, offset=14 and offset=15.

And again it's important to note that the actual registers of the device does not have to be located as we describe them to the world (in the MDF). We can present them in any way we like. 

In the firmware code the **vscp_readAppReg** [callback](./level1_callbacks.md#vscp_readAppReg) will looks something like this

```cpp
uint8_t vscp_readAppReg(uint8_t reg)
{    
    uint8_t rv;

    rv = 0x00; // default read

    // page 0
    if ( 0 == vscp_page_select ) {
        // MSB of temperature 1
        if ( 10 == reg ) {
            rv = ( ( readTempSensor( 1 ) >> 8 ) & 0xff );
	}
        // LSB of temperature 1
        else if ( 11 = reg ) {
            rv = ( readTempSensor( 1 )  & 0xff );
        }
        // MSB of temperature 2
        else if ( 12 = reg ) {
            rv = ( ( readTempSensor( 2 ) >> 8 ) & 0xff );        
        }
        // LSB of temperature 2
        else if ( 13 = reg ) {
            rv = ( readTempSensor( 2 )  & 0xff );        
        }
        // Read interval for temperature 1
        else if ( 14 = reg ) {
            rv = readEEPROM( EEPROM_INTERVAL_SENSOR1 );        
        }
        // Read interval for temperature 2
        else if ( 15 = reg ) {
            rv = readEEPROM( EEPROM_INTERVAL_SENSOR2 );        
        }                                                            
    }
    // Page 1
    else if ( 1 == vscp_page_select ) {
        // do read page 1 stuff here
    }
    
    return rv;
}
```

and the **vscp_writeAppReg** [callback](level1_callbacks.md#vscp_writeAppReg) code will look something like this

```cpp
uint8_t vscp_writeAppReg( uint8_t reg, uint8_t val )
{
    uint8_t rv;

    rv = ~val; // error return
    
    // page 0
    if ( 0 == vscp_page_select ) {
    
        // Read interval for temperature 1
        if ( 14 == reg ) {
            writeEEPROM( EEPROM_INTERVAL_SENSOR1, val );
            rv = readEEPROM( EEPROM_INTERVAL_SENSOR1 );
	}
        // Read interval for temperature 2
        else if ( 14 == reg ) {
            writeEEPROM( EEPROM_INTERVAL_SENSOR2, val );
            rv = readEEPROM( EEPROM_INTERVAL_SENSOR2 );
	}
       
    }
    else if ( 1 == vscp_page_select ) {
        // do write page 1 stuff here
    }
}
```

Note that there is no need to do write routines for the temperature values. They are read only. So we just leav them out.

So now we can read and write registers of our new device. But if the world does not know what to expect from each register, what can be read/written from/to it, our work so far have no use for anyone. The task of the [MDF (Module Description File)](http://docs.vscp.org/spec/latest/#/./vscp_module_description_file) is to define this. 


In the MDF registers are defined between the

```xml
`<registers>`
    ...registers goes here...
`</register>`
```

and each register is defined as

```xml
`<register>`
    ...a register definition goes here...
`</register>`
```


The full format is

```xml
`<reg page="0" offset="15" default="0" >`         
    `<name lang="en">`alsoaname_msb`</name>`         
    `<description lang="en">`MSB of alsoaname`</description>`         
    `<help type="text/url"  lang="en">`what help is this`</help>`         
    `<access>`rw`</access>`     
`</reg>`  
```

with the following meaning for attributes and properties

 | Attributes/Properties | Description | 
 | --------------------- | -----------  | 
 | **page**              | The page the register is located on. (0-65535) | 
 | **offset**            | The offset on the page for the  register (0-127) | 
 | **default**           | The default value for the register. | 
 | **name**              | The name for the register. Choose something descriptive. Note the language attribute. You can have different names for different languages. Languages are defined using ISO country codes.                           | 
 | **description**       | Describe here what the register is used for. Note the language attribute. You can have different descriptions for different languages. Use "\n" for new lines.                                                       | 
 | **help**              | Point to a web page that contains help for this register. Note the language attribute. You can have different help pointers for different languages. Remember also so set a text that describe what the help is for. | 
 | **access**            | Tells if the register can be read "r" or written "w" or both "rw"                                                                            
 
So for the registers we defined above we get something like this

```xml
`<registers>`

    `<!-- MSB temperature sensor 1 -->`
    `<reg page="0" offset="10" default="0">`
        `<name lang="en">`temperature1_msb`</name>`         
        `<description lang="en">`
          MSB of temperature reading for sensor 1.\n
          The temperature is presented as 100*real temperature.
        `</description>`         
        `<help type="http://www.somepage.com/help"  lang="en">`
          Help o page for temperature 1 register
        `</help>`         
        `<access>`r`</access>`
    `</reg>`
    
    `<!-- LSB temperature sensor 1 -->`
    `<reg page="0" offset="11" default="0">`
        `<name lang="en">`temperature1_lsb`</name>`         
        `<description lang="en">`
          LSB of temperature reading for sensor 1.\n
          The temperature is presented as 100*real temperature.
        `</description>`         
        `<help type="http://www.somepage.com/help"  lang="en">`
          Help o page for temperature 1 register
        `</help>`         
        `<access>`r`</access>`
    `</reg>`
    
    `<!-- MSB temperature sensor 2 -->`
    `<reg page="0" offset="12" default="0">`
        `<name lang="en">`temperature2_msb`</name>`         
        `<description lang="en">`
          MSB of temperature reading for sensor 2.\n
          The temperature is presented as 100*real temperature.
        `</description>`         
        `<help type="http://www.somepage.com/help"  lang="en">`
          Help o page for temperature 2 register
        `</help>`         
        `<access>`r`</access>`
    `</reg>`
    
    `<!-- LSB temperature sensor 2 -->`
    `<reg page="0" offset="13" default="0">`
        `<name lang="en">`temperature2_lsb`</name>`         
        `<description lang="en">`
          LSB of temperature reading for sensor 2.\n
          The temperature is presented as 100*real temperature.
        `</description>`         
        `<help type="http://www.somepage.com/help"  lang="en">`
          Help o page for temperature 2 register
        `</help>`         
        `<access>`r`</access>`
    `</reg>`
    
    `<!-- Reading interval temp sensor 1 -->`
    `<reg page="0" offset="14" default="0">`
        `<name lang="en">`temperature1_interval`</name>`         
        `<description lang="en">`
          Reading interval in second for temperature sensor 1
          Set to zero to turn of.
        `</description>`         
        `<help type="http://www.somepage.com/help"  lang="en">`
          Help page for temperature 1 reading interval register
        `</help>`         
        `<access>`rw`</access>`
    `</reg>`

    `<!-- Reading interval temp sensor 2 -->`
    `<reg page="0" offset="15" default="0">`
        `<name lang="en">`temperature2_interval`</name>`         
        `<description lang="en">`
          Reading interval in second for temperature sensor 2
          Set to zero to turn of.
        `</description>`         
        `<help type="http://www.somepage.com/help"  lang="en">`
          Help page for temperature 2 reading interval register
        `</help>`         
        `<access>`rw`</access>`
    `</reg>`
            
`</registers>`
```

So that's it. You're registers are defined and presented to the world. The MDF need some more info to be complete. Here is a dummy MDF you an start out with. Just add your registers and your own information to it.

```xml
`<?xml version = "1.0" encoding = "UTF-8" ?>`
`<vscp>`
    `<module>`
        `<name>`Name for your device`</name>`
        `<model>`model for your device`</model>`
        `<version>`1.0.0`</version>`
        `<changed>`2011-11-11`</changed>`
        `<description lang="en">`
            This module can bla bla bla bla
        `</description>`
        `<infourl>`
            http://www.somwhere.com
        `</infourl>`
        `<buffersize>`8`</buffersize>`
        `<manufacturer>`
            `<name>`Your company or name`</name>`
            `<address>`
                `<street>`Your street`</street>`
                `<city>`your city`</city>`
                `<postcode>`your postcode`</postcode>`
                `<country>`Your countr`</country>`
            `</address>`
            `<telephone>`
                `<number>`+46 123 45678`</number>`
                `<description lang="en" >`Main Reception`</description>`
            `</telephone>`
            `<email>`
                `<address>`support@somewhere.com`</address>`
                `<description>`Support email`</description>`
            `</email>`
            `<email>`
                `<address>`sales@somewhere.com`</address>`
                `<description>`Sales inquires email`</description>`
            `</email>`
            `<email>`
                `<address>`info@somewhere.com`</address>`
                `<description>`General email`</description>`
            `</email>`	
            `<web>`
                `<address>`http://www.somewhere.com`</address>`
                `<description>`Main web site`</description>`
            `</web>`					
        `</manufacturer>`  
	
        `<!-- Picture of device (add more if needed) -->`
        <picture 
           path="http://www.somewhere.com/images/picture.jpg" 
           format="jpg"
           size="12345">
           `<description lang="en" >`
              Picture of my device.
           `</description>`
        `</picture>`

        `<!-- Firmware for the device (add more if needed)-->`
        <firmware 
            path="http://www.somwhere.com/device.hex" 
            format="intelhex8"
            size="32000"
            date="2015-01-00"
            version_major="1"
            version_minor="0"
            version_subminor="0">
            `<description lang="en" >`
                Firmware for my device.
            `</description>` 
        `</firmware>`

	<!-- Full documentation for the device (add more if needed) -->
	<manual path="http://www.somewhere.com/device.pdf" 	
            lang="en"
            format="pdf">
            `<description lang="en" >`
                Full manual for my device
            `</description>`
        `</manual>`
	
        `<boot>`
            `<algorithm>`1`</algorithm>`
            `<blocksize>`8`</blocksize>`
            `<blockcount>`4096`</blockcount>`
        `</boot>`
        
        `<!-- Register definitions goes here -->`
        `<registers>`
        `</registers>`
        
        `<!-- Abstractions goes here -->`
        `<abstractions>`
        `</abstractions>`
        
        `<!-- Alarm definitions goes here -->`
        `<alarm>`
        `</alarm>`
        
        `<!-- Events the module can send goes here -->`
        `<events>`
        `</events>`
        
    `</module>`	
    
`</vscp>`	
```

You can now load this file in VSCP works configuration view or if you have set it in the MDF url of the device and uploaded it to a server somewhere it will be read in automatically.

Bad thing here is that the temperature values are separated into two  registers. [Abstractions](level1_abstractions.md) is the answer but before you attend to them you probably should add all the required [callbacks](level1_callbacks.md) to your code.


[filename](./bottom_copyright.md ':include')
