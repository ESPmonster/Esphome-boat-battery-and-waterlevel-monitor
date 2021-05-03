# Esphome boat/car battery and waterlevel-monitor
A boat/car battery voltage and water level monitor.

Based on the work of Chris2b (https://github.com/chrisb2/battery-monitor), translated from .ino to ESPhome. 

I have used an ESP32 (MH et Live devkit) with ESPhome to monitor boat (or car) battery voltage and water level (depth). Because the boat is located outside my home assistant network but inside another known wifi network I could use MQTT to deliver the data to Thingspeak. Thingspeak has an unencrypted MQTT option (https://nl.mathworks.com/help/thingspeak/mqtt-basics.html). I couldn't find any correct ESPhome MQTT configuration so had to find out for myself. The script below can therefore be used for others considering using thingspeak via MQTT.

The usercase here is that I have a big open boat that could quickly fill up with rainwater. A pump is connected to a 12V car battery with a solar panel. We don't yet know when we have enough solar energy to keep the boat dry. The ESP is powered from the battery and reports battery voltage. The device enters deep sleep to preserve power. An ultrasonic distance sensor (JSN-SR04T) and a float switch are also attached to detect a rising water level inside the boat (i.e., pump failure).

When a critical value is reached, a React in Thingspeak triggers a (ThingHTTP) webhook that allows IFTT (if this then that) to notify me via the IFTT app and also sends an email.

An ESP32 was used because it should have superior wifi and allows 5V on the VIN pin.

Update 05-2021: I replaced the analog to digital voltage sensor because its values varied strongly. Instead, I now use a INA226 voltage sensor: much better! Also, I added a float switch because the ultrasonic sensor had some trouble getting reliable measurements in the messy hull. Because the float switch suffered from floating in the off state, I added a 47k pull-down resistor. The ultrasonic sensor has now also been fixed by placing the ultrasonic sensor in large (110 mm diameter) sewage pipe with the sensor placed in a (conus-shaped) funnel to direct the sound in the right direction (see photographs).

## circuit
The circuit was based on the one from Cris2b but the voltage divider to directly measure voltage was later removed because of poor measurement precision. I also added the ultrasonic sensor, ina226, and float switch.

Chris2B's circuit:

![project](battery-monitor%20circuit%20Chris2B.png?raw=true "Circuit")


Let me know if you want to see my exact circuit.

## ESPhome configuration

See the file

## BOM

* ESP32 MH et Live Devkit
* Traco Power [TSR-1-2450](docs/tsr1.pdf) DC/DC Step-Down Converter
* 1N4747 Zener Diode, 20V, 1 Watt
* 1000uF 35V Electrolytic Capacitor
* 0.1uF 50V Ceramic Capacitor
* 150uH RF Choke, 4.2&Omega;, 175mA max
* 47K&Omega; Resistor
* In-line fuse holder and 250mA fuse
* 2 core cable (2 Meters)
* Solder tags (6mm ID) x 2 (if you want to connect directly to a 12V battery)
* IP66 waterproof case (sonoff)
* screw terminal block 2x
* 40 Pins header Female (to easily remove the ESP from the board)
05-2021 added:
* INA226 voltage sensor
* water float switch (https://www.amazon.com/NUZAMAS-Automatic-Caravan-Camping-Fishing/dp/B073XDXM9P)
* 110 mm diameter sewage pipe
* plywood, hot glue, some screws


## completed project

![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/20210430_101307.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/20210430_101206.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/IMG-20210502-WA0011.jpeg?v=4&s=400)
Previous version:
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/raw/main/20210306_152855.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/raw/main/20210306_152841.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/raw/main/20210306_153557.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/raw/main/20210306_153755.jpg?v=4&s=400)


## References

Based on the work of:
* Chris2B (https://github.com/chrisb2/battery-monitor)
