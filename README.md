# uScript
A very basic script interpretter. It provides a very basic scripting layer to call C code in the user application. Functions and Variables get registered with the engine using token strings so they can be accessed in the script. 

## Example:

script file
```bash
#event driven code block
@( $TEMP > 50)
{
    ALERT("It's Hot!")
}


#sequential block, repeats

if( $HUM > 60)
{
    ALERT("IT's humid!")    #send message
    LED(1) #turn led on
}
DELAY(15000)

```

main.c
```c
#include "uscript.h"

uscript_engine_t engine;

int temperature; 
int humidity;


//Create functions that can be accessed from the script 
void sendAlert(int argc, char* argv[])
{
    char* msg = argv[1];

    //CODE to send message stored in msg
}

void setLed(int argc, char* argv[])
{   
    //parse int from arg 1
    int val = atoi(argv[1]);
    
    if(val ==1)
    {
        //CODE to turn on led 
    }
    else
    {
        //CODE to turn off led
    }
}


/**
 * Example of a callback that would periodically give new values
 */
void async_sensor_callback(int newTemp, int newHum)
{
    temperature = newTemp;
    humidity = newHum;

    //let script engine know that variables updated for event blocks
    uscript_update(&engine, "TEMP");
    uscript_update(&engine, "HUM");
}


main()
{
    //Initialize the script engine, tell it you will be registering 2 functions and 2 variables
    uscript_engine_init(&engine, 2,2 );
    
    //register functions
    uscript_reg_function(&engine, "ALERT", sendAlert);
    uscript_reg_function(&engine, "LED", setLed);

    //register variables to make them accessible
    uscript_reg_var(&engine, "TEMP", &temperature);
    uscript_reg_var(&engine, "HUM", &humidity);


    //load script as text
    uscript_load(&engine, "script.u")

    //Start sensors
    // CODE to start async sensors

    while(1)
    {
        //continue script
        uscript_cont(&engine);
    }

}
```
