# Esphome boat/car battery and waterlevel-monitor with remote pump control via MQTT
A boat/car battery voltage and water level monitor with Telegram alerting, deep sleep and remote pump controls.

Based on the work of Chris2b (https://github.com/chrisb2/battery-monitor) to power the ESP from the battery, translated from .ino to ESPhome. 

I have used an ESP32 with ESPhome to monitor a boat (or car) battery voltage and water level (depth). Because the boat is located outside my home assistant network but within reach of another known wifi network I could use MQTT via WIFI to deliver the data to Thingspeak. Thingspeak has an unencrypted MQTT option (https://nl.mathworks.com/help/thingspeak/mqtt-basics.html). I couldn't find any correct ESPhome MQTT configurations for it so had to find out myself. The script below can therefore be used for others considering using thingspeak for ESPhome-based devices via MQTT. (Update 12-2021: I have updated the configuration so that it still works with the new MQTT3 thingspeak server.)

The usercase here is that we have a big open boat that could quickly flood with rainwater. A pump is connected to a 12V car battery with a solar panel but we don't yet know when we have enough solar energy to keep the boat dry. And we want to know if this system fails (happened before, cost us a new starter motor). The device enters deep sleep to preserve power; it is woken up every 1.5h or if the float switch is triggered (i.e., high water level on board). An ultrasonic distance sensor (JSN-SR04T) is also attached to quantify the water level inside the boat (i.e., pump failure). I replaced the original analog to digital voltage sensor because its values varied strongly. Instead, I now use a INA226 voltage sensor: much better! Also, Because the float switch suffered from pin floating in the off state, I added a 47k pull-down resistor. The ultrasonic sensor has now also been fixed (suffered from poor measurements becuase of its wide projection angle in a messy boat hull) by placing the ultrasonic sensor in a large (110 mm diameter) sewage pipe with the sensor placed in a (conus-shaped) funnel to direct the sound in the right direction (see photographs).

When a critical value is reached, a React within Thingspeak triggers a (ThingHTTP) webhook that allows IFTT (if this then that www.IFTT.com) to notify us via Telegram. Also the ESP makes HTTP_get requests to healthchecks.io to confirm that it's 'alive' (heartbeat monitor); if it fails >4h I'm notified as well. We build a dashboard on thingspeak that can easily  be viewed on your mobile phone via the Thingview app (https://play.google.com/store/apps/details?id=com.cinetica_tech.thingview&hl=en&gl=US).

The ESP is now also subscribed to a couple of thingspeak fields. Using MQTTX or HTTP requests to publish command values to these fields we can 1) manually activate the pump 2) prevent deep sleep 3) start deep sleep and 4) reboot, all via MQTT. It is important to realize that any MQTT publications to incorrect topics will result in a disconnect from the Thingspeak server.


## circuit
The circuit was based on the one from Cris2b but the voltage divider to directly measure voltage was later removed because of poor measurement precision. It brings down the 12V from the battery to 5V and protects the circuit to any voltage spikes and shorts. I also added the ultrasonic sensor, ina226 voltage sensor, and float switch.

![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/Wiring%20diagram.png?v=4&s=400)

Edit: the wiring diagram has been modified so that the float switch can also directly activate the pump and the pump can be activated via mqtt through a relay. Let me know if you need a new wiring diagram.

## ESPhome configuration

See the file

## BOM

* ESP32 MH et Live Devkit (should have superior wifi and allows 5V on the VIN pin)
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
* 40 female pin headers  (to easily remove the ESP from the board)
* jsn-sr04t ultrasonic distance sensor

05-2021 added:
* INA226 voltage sensor
* water float switch (https://www.amazon.com/NUZAMAS-Automatic-Caravan-Camping-Fishing/dp/B073XDXM9P)
* 110 mm diameter sewage pipe
* plywood, hot glue, some screws


## completed project

![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/Thingview%20app.jpg?v=2&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/20210430_101307.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/20210430_101206.jpg?v=4&s=400)
![](https://github.com/ESPmonster/Esphome-boat-battery-and-waterlevel-monitor/blob/main/IMG-20210502-WA0011.jpeg?v=4&s=400)


## References

Circuit was based on the work of:
* Chris2B (https://github.com/chrisb2/battery-monitor)
