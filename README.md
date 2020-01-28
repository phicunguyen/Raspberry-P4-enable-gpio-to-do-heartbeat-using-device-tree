# Raspberry-P4-enable-gpio-to-do-heartbeat-using-device-tree
Using the device tree on Raspberry Pi to enable heartbeat on GPIO5 and GPIO6

Here is the device tree to enable the heartbeat on gpio5 and gpio6.

    /dts-v1/;
    /plugin/;

    / {
        compatible = "brcm,bcm2708";

        fragment@0 {
            target = <&leds>;
            __overlay__ {
                my_led0: myled0 {
                    label = "MYLED0";
                    linux,default-trigger = "heartbeat";
                    gpios = <&gpio 5 0>;
                };
            };
        };
        fragment@1 {
            target = <&leds>;
            __overlay__ {
                 my_led1: myled1 {
                    label = "MYLED1";
                    linux,default-trigger = "heartbeat";
                    gpios = <&gpio 6 0>;
                };
            };
        };
    };

Let's call this heartbeat.dts 

1. Generate the heartbeat.dtb from heartbeat.dts by run the command below on your raspberry pi
    
        dtc -@ -I dts -O dtb -o heartbeat.dtb heartbeat.dts
        
2. Copy the heartbeat.dtb to your /boot/overlays

        sudo cp heartbeat.dtb /boot/overlays
        
3. Add the "dtoverlay=heartbeat" at the end of your /boot/config.txt. (sudo vi /boot/config.txt) 

        dtoverlay=heartbeat
        
4. reboot your raspberry pi
      
        sudo reboot         
        
5. Put a scope probe on your raspberry pi J8-29 (gpio5) AND J8-31 (gpio6). You should see some activities on the pin.

MYLED0 and MYLED1 is on /sys/class/leds after reboot the system.

        pi@raspberrypi:~/phi/dts $ ls /sys/class/leds/
        led0  led1  mmc0::  MYLED0  MYLED1
        
You can stop the heartbeat by echo none to trigger. since it's write protected you need to do sudo. 

    sudo sh -c "echo none > /sys/class/leds/MYLED0/trigger"
    
You can start the heartbeat again by echo heartbeat to trigger.

    sudo sh -c "echo heartbeat > /sys/class/leds/MYLED0/trigger"   

Same thing above can be done for MYLED1.
    
There is an led called ACT, you see it blinking while the raspberry is bootup and stop after boot up is done.
You can control the led from your raspberry pi after boot up is finish
Run the command below on your raspberry pi to see the led blinking.
    
    sudo sh -c "echo heartbeat >/sys/class/leds/led0/trigger"						
    
Have fun.
