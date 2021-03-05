# Esphome-boat-battery-and-waterlevel-monitor
A boat/car battery monitor for battery voltage and water level.

Based on the work of Chris2b (https://github.com/chrisb2/battery-monitor).

I have used an ESP32 (MH et Live devkit) with ESPhome to monitor boat (or car) battery voltage and water level (depth). Because the boat is located outside my home assistant network but inside another known wifi network I could use MQTT to deliver the data to Thingspeak. Thingspeak has an unencrypted MQTT option. I couldn't find any correct ESPhome MQTT configuration so had to find out for myself. The script below can therefore be used for others considering using thingspeak via MQTT.

The usercase here is that I have a big open boat that could quickly fill up with rainwater. A pump is connected to a 12V car battery with a solar panel. We don't yet know when we have enough solar energy to keep the boat dry. The ESP is powered from the battery and reports battery voltage. The device enters deep sleep to preserve power. An ultrasonic distance sensor (JSN-SR04T) is also attached to detect a rising water level inside the boat (i.e., pump failure).

When a critical value is reach, a React in Thingspeak triggers a (ThingHTTP) webhook that allows IFTT (if this then that) to notify me via the IFTT app and also sends an email.

