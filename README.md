# Hacking an iSP100 for ESPHome

## Description

On April 2, 2022, iHome discontinued its Control Cloud Service, making the
iHome iSP100 smart plugs bricked for most users. I decided to see if I
could use ESPHome to control this smart plug instead. Unfortunately,
the iSP100 uses the AzureWave AW-CU300, so it's not a simple firmware
swap. However, the iSP100 has enough space inside to fit a NodeMCU
ESP8266 board.

Below is an outline of the surgery I did to convert my iHome smart plug
into an ESPHome device.

## iSP100 Board

![A picture of the board inside an iSP100 smart plug showing the jumper
connections to the AzureWave AW-CU300.](isp100.png)

With AW-CU300 removed, we are interested in J9 on the main board:

|Pin|Description|
|:--|:--|
|J9-1|Relay and white LED on the side button board, active high, buffered by Q1|
|J9-2|Plug detect, photodiode, 0V: plug, >1V:no plug|
|J9-3|Side button, needs interal pullup|
|J9-4|Red LED on the side button board|
|J9-5|Green LED on the side button board|
|J9-6|3.3V|
|J9-7|GND|

For the NodeMCU swap, I chose to drive it directly on pin 3V3 from
the 3.3V supply produced by the power supply main board. The AW-CU300
and ESP8266 power consumption and voltage tolerances are close
enough that there aren't any issues.

Avoid connecting J9-1 to a digital output that might be have a dual
purpose, like blinking an LED during serial communication.

## ESPHome yaml

This [iSP100 yaml file](isp100.yaml) shows how I wired J9 to my NodeMCU
board. The side button toggles the relay to control output power.
The green LED means WiFi is connected, and red LED means that WiFi isn't
connected. This doesn't perfectly mimic the iHome behavior, but it's
close enough.

## Results

![A picture of the board inside an iSP100 smart plug with the
replacment NodeMCU microcontroller.](isp100-hacked.png)

It works. This probably isn't the most cost efficient way to make a smart
plug. I didn't do anything with the plug detection feature. Ideally,
you would use this to lower power consumption when there isn't anything
to control.

## Disclaimer

This project involves working with hazardous voltages (voltages greater
than 50V, which can cause severe injury or death.)
